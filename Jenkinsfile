pipeline {
  agent any
  
  environment {
    JIRA_CREDENTIALS_ID = 'jira_cred' // ID of Jenkins credentials for Jira
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Your Jira instance URL
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
          withEnv(['JIRA_SITE=jira']) {
            // Provide a comment body for the jiraAddComment step
            jiraAddComment idOrKey: 'MET-3', comment: 'Test comment from Jenkins' // Corrected syntax
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          def issueKey = 'MAT-2' // Jira issue key to update
          def transitionId = '31' // Transition ID for "Done"

          // Transition the Jira issue to "Done"
          jiraTransitionIssue(
            issueSelector: [issueKey: issueKey], 
            transitionId: transitionId,
            jiraUrl: JIRA_BASE_URL,
            credentialsId: JIRA_CREDENTIALS_ID
          )
          
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
