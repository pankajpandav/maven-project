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
              slackSend (channel: "#slack-channel", color: '#4286f4', message: "Deploy Approval: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
                              script {
                                  try {
                                      timeout(time:30, unit:'MINUTES') {
                                          env.APPROVE_PROD = input message: 'Deploy to Production', ok: 'Continue',
                                              parameters: [choice(name: 'APPROVE_PROD', choices: 'YES\nNO', description: 'Deploy from STAGING to PRODUCTION?')]
                                          if (env.APPROVE_PROD == 'YES'){
                                              env.DPROD = true
                                          } else {
                                              env.DPROD = false
                                          }
                                      }
                                  } catch (error) {
                                      env.DPROD = true
                                      echo 'Timeout has been reached! Deploy to PRODUCTION automatically activated'
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
