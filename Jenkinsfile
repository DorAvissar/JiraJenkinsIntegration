pipeline {
  agent any
  
  environment {
    JIRA_CREDENTIALS_ID = 'jira_cred' // ID of Jenkins credentials for Jira
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Your Jira instance URL
  }
  
  stages {
    stage('Add Comment to Jira') { // This is a named stage
      steps {
        script {
          withEnv(['JIRA_SITE=jira']) {
            jiraAddComment(idOrKey: 'MET-3', input: 'comment DOR') // Corrected syntax
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') { // This is another named stage
      steps {
        script {
          def issueKey = 'MET-2' // The Jira issue key to update
          def transitionId = '31' // The transition ID for "Done"

          // Transition the Jira issue to "Done" using the transition ID
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
      echo 'Pipeline completed.' // This runs after all stages
    }
  }
}
