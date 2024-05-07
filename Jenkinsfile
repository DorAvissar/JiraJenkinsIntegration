pipeline {
  agent any
  
  environment {
    JIRA_CREDENTIALS_ID = 'jira_cred' // Jenkins credentials ID for Jira
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Jira instance URL
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
          def issueKey = 'MET-3' // The Jira issue key
          def transitionName = 'Done' // The transition name for "Done"

          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            jiraTransitionIssue(
              issueSelector: [key: issueKey], // Corrected parameter
              transitionName: transitionName, // Use transition name instead of ID
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
