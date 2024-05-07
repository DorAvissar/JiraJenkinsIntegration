pipeline {
  agent any
  
  environment {
    JIRA_CREDENTIALS_ID = 'jira_cred' // Jenkins credentials ID for Jira
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Your Jira instance URL
    JIRA_SITE_NAME = 'jira' // Jira site name configured in Jenkins
  }
  
  stages {
    stage('Hello World') {
      steps {
        sh 'echo "Hello World!"'
      }
    }

    stage('Add Comment to Jira') {
      steps {
        script {
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            jiraAddComment(idOrKey: 'MET-3', comment: 'Test comment from Jenkins')
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          def issueKey = 'MET-2' // Jira issue key to update
          def transitionId = '31' // Transition ID for "Done"

          // Ensure the environment is correctly set
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            jiraTransitionIssue(
              issueSelector: [key: issueKey], 
              transitionId: transitionId,
              jiraUrl: JIRA_BASE_URL,
              credentialsId: JIRA_CREDENTIALS_ID
            )
          }
          
          echo "Attempted to transition Jira issue ${issueKey} to Done."
        }
      }
    }
  }

  post {
    always {
      echo 'Pipeline completed.'
    }
  }
}
