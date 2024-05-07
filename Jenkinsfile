pipeline {
  agent any
  stages {
    stage('Hello World') {
      steps {
        sh 'echo "Hello World!"' // Simple output to confirm the pipeline runs
      }
    }

    stage('Change Jira Issue Status') { // Change stage name to reflect new operation
      steps {
        script {
          def issueKey = 'MET-3' // Jira issue key to change status
          def transitionName = 'Done' // Transition name to move to "Done"
          
          // Change the Jira issue status to "Done"
          jiraTransitionIssue issueKey: issueKey, transitionName: transitionName, 
                              jiraUrl: JIRA_BASE_URL, credentialsId: JIRA_CREDENTIALS_ID
                              
          echo "Jira issue ${issueKey} status changed to '${transitionName}'."
        }
      }
    }
  }

  post {
    always {
      echo 'Pipeline completed.' // Output to indicate the pipeline finished
    }
  }
}
