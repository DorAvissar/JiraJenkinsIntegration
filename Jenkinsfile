pipeline {
    agent any // Use any available Jenkins agent/worker
    stages {
        stage('Hello World') { // A simple stage for demonstration
            steps {
                sh 'echo "Hello World!"' // Echo a message to confirm pipeline starts
            }
        }

        stage('Checkout') { // Stage to check out the Git repository
            steps {
                // Checkout the repository and determine the branch name
                script {
                    def scmVars = checkout scm
                    def branchName = scmVars.BRANCH_NAME
                    echo "Checked out branch: ${branchName}"
                }
            }
        }

        stage('Check Jira Issue') { // Stage to check Jira issue based on branch name
            steps {
                script {
                    // Regex pattern to identify a Jira issue key (e.g., EX-123)
                    def jiraPattern = ~/^[A-Z]+-\d+$/
                    def branchName = env.BRANCH_NAME // Get the branch name from environment variables

                    if (branchName == null) {
                        error("Branch name is not available")
                    }

                    if (jiraPattern.matcher(branchName).matches()) { // Check if it matches the Jira pattern
                        echo "Branch name ${branchName} matches Jira issue key pattern"
                        // Assuming you have a Jenkins plugin for Jira interaction
                        // Update Jira issue to "Done"
                        jiraIssueUpdate(branchName, 'Done') // This is a fictional method; replace with actual code
                    } else {
                        echo "Branch name ${branchName} does not match Jira issue key pattern"
                    }
                }
            }
        }
    }
}

def jiraIssueUpdate(issueKey, newStatus) {
    // Requires the Jenkins Jira Plugin
    jiraIssueSelector = [$class: 'IssueSelector$IssueKey', issueKey: issueKey]
    
    // Transition the Jira issue to the new status
    jiraTransition = [$class: 'Transition', name: newStatus]
    
    // Update the Jira issue's status
    jiraSubmit = jiraIssueSelector & jiraTransition
    
    jiraSubmit.apply() // This triggers the update
    echo "Updated Jira issue ${issueKey} to status: ${newStatus}"
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
