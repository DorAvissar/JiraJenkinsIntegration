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
          def lastCommitMessage = sh(script: 'git log -1 --merges --pretty=format:%s', returnStdout: true).trim()
          
          // Extract the branch name from the commit message if it's a merge commit
          def mergeBranchPattern = ~/Merge pull request #(.*?) from (.*)/
          def matcher = lastCommitMessage =~ mergeBranchPattern
          
          if (matcher.find()) {
            // The branch name from which the pull request originated
            env.JIRA_ISSUE_KEY = matcher.group(2).trim()
          } else {
            error("No merge found into 'main'. Unable to determine Jira issue key.")
          }
        }
      }
    }

    stage('Add Comment to Jira') {
      steps {
        script {
          if (env.JIRA_ISSUE_KEY) {
            withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
              jiraAddComment(idOrKey: env.JIRA_ISSUE_KEY, comment: 'Test comment from Jenkins')
            }
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          if (env.JIRA_ISSUE_KEY) {
            def transitionName = 'Done' // Transition name for "Done"

            withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
              jiraTransitionIssue(
                issueSelector: [key: env.JIRA_ISSUE_KEY], 
                transitionName: transitionName,
                jiraUrl: JIRA_BASE_URL,
                credentialsId: JIRA_CREDENTIALS_ID
              )
            }
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
