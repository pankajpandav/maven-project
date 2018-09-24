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
                    slackSend channel: "#Builds", color: '#4286f4', message: "Deploy Approval: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", attachments: "[{'fallback': 'You are unable to decide', 'callback_id': 'env.APPROVE_PROD', 'color': '#3AA3E3', 'attachment_type': 'default', 'actions': [ { 'name': 'approval', 'text': 'Approve', 'type': 'button', 'value': 'YES' }, { 'name': 'approval', 'text': 'Decline', 'type': 'button', 'value': 'NO' }]}]"
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
