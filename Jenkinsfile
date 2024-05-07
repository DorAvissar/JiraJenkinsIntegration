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

    stage('Get Last Merged Branch Name') {
      steps {
        script {
          // Get the last commit message
          def lastCommitMessage = sh(script: 'git log -1 --pretty=format:%s', returnStdout: true).trim()
          
          // Extract the branch name from the merge commit message (if it indicates a merge)
          def mergeBranchPattern = ~/Merge branch '(.*)' into main/
          def matcher = lastCommitMessage =~ mergeBranchPattern
          
          if (matcher.find()) {
            // The branch name that was merged
            env.JIRA_ISSUE_KEY = matcher.group(1).trim() // Define environment variable for the Jira issue key
          } else {
            error("No merge found into 'main'. Unable to determine Jira issue key.")
          }
        }
      }
    }

    stage('Add Comment to Jira') {
      steps {
        script {
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            // Use the derived Jira issue key
            jiraAddComment(idOrKey: env.JIRA_ISSUE_KEY, comment: 'Test comment from Jenkins')
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
            def transitionName = 'Done' // Use transition name instead of ID

            jiraTransitionIssue(
              issueSelector: [key: env.JIRA_ISSUE_KEY], // Use the derived issue key
              transitionName: transitionName,
              jiraUrl: JIRA_BASE_URL,
              credentialsId: JIRA_CREDENTIALS_ID
            )
          }
          
          echo "Attempted to transition Jira issue ${env.JIRA_ISSUE_KEY} to Done."
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
