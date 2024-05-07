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
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) { // Ensure correct Jira site name
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

          // Ensure the correct environment variable is set
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            jiraTransitionIssue(
              issueSelector: [key: issueKey], // Corrected syntax for issueSelector
              transitionId: transitionId,
              jiraUrl: JIRA_BASE_URL,
              credentialsId: JIRA_CREDENTIALS_ID
            )
          }
          
          echo "Jira issue ${issueKey} transitioned to Done (ID: ${transitionId})."
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
