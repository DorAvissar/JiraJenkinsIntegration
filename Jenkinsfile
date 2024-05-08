pipeline {
  agent any
  
  environment {
    JIRA_BASE_URL = 'http://172.206.241.10:8080/' // Jira instance URL
    TRANSITION_ID = '31' // Transition ID for "Done"
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
          def lastCommitMessage = sh(script: 'git log -1 --pretty=format:%s', returnStdout: true).trim()

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

    stage('Transition Jira Issue to Done') {
      steps {
        script {
          def issueKey = env.JIRA_ISSUE_KEY

          if (issueKey) {
            // Use 'withCredentials' to securely get credentials
            withCredentials([usernamePassword(credentialsId: 'jira_cred', passwordVariable: 'JIRA_PASSWORD', usernameVariable: 'JIRA_USER')]) {
              sh """
                curl -X POST \
                -u \${JIRA_USER}:\${JIRA_PASSWORD} \
                -H 'Content-Type: application/json' \
                -d '{
                  "transition": {
                    "id": "${TRANSITION_ID}"
                  },
                  "update": {
                    "comment": [
                      {
                        "add": {
                          "body": "Transitioning to Done from Jenkins Pipeline."
                        }
                      }
                    ]
                  }
                }' \
                \${JIRA_BASE_URL}/rest/api/2/issue/\${issueKey}/transitions
              """
            }
          } else {
            error("Cannot transition Jira issue to Done. The JIRA_ISSUE_KEY environment variable is missing.")
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
