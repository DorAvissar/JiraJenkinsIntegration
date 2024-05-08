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
          def issueKey = env.JIRA_ISSUE_KEY

          if (issueKey) {
            withEnv(['JIRA_SITE=' + JIRA_SITE_NAME]) {
              jiraAddComment(idOrKey: issueKey, comment: 'Test comment from Jenkins')
            }
          } else {
            error("Jira issue key is null. Cannot add comment.")
          }
        }
      }
    }

    stage('Transition Jira Issue to Done') {
      steps {
          def issueKey = env.JIRA_ISSUE_KEY
          withEnv(['JIRA_SITE=jira']) {
            def transitionInput =
            [
                transition: [
                    id: '31'
                ]
            ]

            jiraTransitionIssue idOrKey: issueKey, input: transitionInput
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

