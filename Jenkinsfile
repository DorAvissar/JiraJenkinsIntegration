pipeline {
    agent any // Use any available Jenkins agent/worker   
    environment {
        JIRA_URL = 'http://172.206.241.10:8080/' // Your Jira instance URL
        JIRA_CREDENTIALS_ID = 'jira_cred' // The ID of your Jira credentials in Jenkins
    }

    stages {
        stage('Hello World') {
            steps {
                sh 'echo "Hello World!"' // Basic stage to confirm pipeline starts
            }
        }

        stage('Extract Branch Name') {
            steps {
                script {
                    // Extract the branch name from the last merge
                    def branchName = sh(script: "git log -1 --pretty=%D | cut -d ',' -f 1", returnStdout: true).trim()
                    echo "Extracted Branch Name: ${branchName}"
                    env.BRANCH_NAME = branchName
                }
            }
        }

        stage('Check Jira Issue') {
            steps {
                script {
                    // Check if the extracted branch name matches a Jira issue key pattern
                    def jiraIssuePattern = ~/^[A-Z]+-\d+$/
                    def branchName = env.BRANCH_NAME
                    echo "Checking branch name against Jira issue pattern: ${branchName}"

                    if (jiraIssuePattern.matcher(branchName).matches()) {
                        // You need to use a custom Groovy script or a Jenkins plugin to interact with Jira
                        def jira = new JiraAPI(JIRA_URL, JIRA_CREDENTIALS_ID) // Custom class or plugin-based
                        def issue = jira.getIssue(branchName)

                        if (issue) {
                            echo "Success: Jira issue exists: ${branchName}"
                        } else {
                            echo "Not Exists: No Jira issue found for: ${branchName}"
                        }
                    } else {
                        echo "Branch name does not match a Jira issue key pattern: ${branchName}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed"
        }
    }
}


// pipeline {
//   agent any
//   stages {
//     stage('add a comment to issue') {
//       steps {
//         jiraComment(issueKey: 'MET-1', body: 'from jenkkins')
//       }
//     }

//   }
// }
