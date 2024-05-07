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
          // Get the last commit message and extract the branch name
          def lastCommitMessage = sh(script: 'git log -1 --pretty=format:%s', returnStdout: true).trim()

          // Define a pattern to extract the branch name from the commit message
          def mergeBranchPattern = ~/Merge pull request #\d+ from (.+)/
          def matcher = lastCommitMessage =~ mergeBranchPattern

          if (matcher.find()) {
            env.JIRA_ISSUE_KEY = matcher.group(1).trim()
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
            def issueKey = env.JIRA_ISSUE_KEY
            jiraAddComment(idOrKey: issueKey, comment: 'Test comment from Jenkins')
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          def issueKey = env.JIRA_ISSUE_KEY // Derived Jira issue key
          def transitionName = 'Done' // Transition name for "Done"

          if (issueKey && transitionName) {
            withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
              jiraTransitionIssue(
                issueSelector: [key: issueKey], // Corrected parameter
                transitionName: transitionName,
                jiraUrl: JIRA_BASE_URL,
                credentialsId: JIRA_CREDENTIALS_ID
              )
            }
          } else {
            error("Missing required information: issueKey or transitionName is null.")
          }
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
