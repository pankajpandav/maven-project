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
          slackSend (channel: "#Builds", color: '#4286f4', message: "Deploy Approval: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'" )
            script
            {
              try
              {
                timeout(time:30, unit:'MINUTES')
                {
                  env.APPROVE_PROD = input message: 'Deploy to Production', ok: 'Continue',
                    parameters: [choice(name: 'APPROVE_PROD', choices: 'YES\nNo', description: 'Deploy from Staging to Production?')]
                  if (env.APPROVE_PROD == 'YES')
                  {
                    env.PROD=true
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
