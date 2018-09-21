pipeline {
  agent any
  stages
  {
    stage('Build')
    {
      steps{
        sh 'mvn clean package'
          }
      post {
        success {
          echo 'Now Archiving..'
          archiveArtifacts artifacts: '**/target/*.war'
                }
            }
    }
    stage('Deploy to Production')
    {
      steps
      {

            script
            {
              try
              {
                timeout(time:10, unit:'MINUTES')
                {
                    env.APPROVE_PROD = input message: 'Deploy to Production', ok: 'Continue',
                    parameters: [choice(name: 'APPROVE_PROD', choices: 'YES\nNO', description: 'Deploy from STAGING to PRODUCTION?')]

                   if (env.APPROVE_PROD == 'YES')
                    {
                      env.PROD=true
                      echo "Approved!"
                    }
                    else
                    {
                      env.PROD = false
                    }
                }
               } catch (error)
               {
                  env.PROD = false
                  echo 'Timeout has been reached! Not going ahead with deployment.'
                }
              }
        }
      }
    }
}
