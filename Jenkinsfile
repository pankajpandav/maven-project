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
          slackSend (channel: "#Builds", color: '#4286f4', message: "Deploy Approval: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", attachments: "{
            "text": "",
            "fallback": "You are unable to decide",
            "callback_id": "env.APPROVE_PROD",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "approval",
                    "text": "Approve",
                    "type": "button",
                    "value": "YES"
                },
                {
                    "name": "approval",
                    "text": "Decline",
                    "type": "button",
                    "value": "NO"
                }
                ]}"" )
            script
            {
              try
              {
                timeout(time:30, unit:'MINUTES')
                {
                  if (env.APPROVE_PROD == 'YES')
                  {
                    env.PROD=true
                    echo "Deployment was approved, Proceeding with it..!"
                  }
                  else
                  {
                    env.PROD = false
                    echo "Deployment was not approved. aborting!"
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
