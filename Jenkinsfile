pipeline {
  agent any
  
  environment {
    JIRA_CREDENTIALS_ID = 'jira_cred' // Jenkins credentials ID for Jira
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Jira instance URL
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
          withEnv(['JIRA_SITE=jira']) { // Ensure the correct Jira site is referenced
            // Corrected syntax for adding a comment to a Jira issue
            def comment = [ body: 'Test comment from Jenkins' ]
            jiraAddComment idOrKey: 'MET-3', body: comment.body
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          def issueKey = 'MET-2' // Jira issue key to update
          def transitionId = '31' // Transition ID for "Done"

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
