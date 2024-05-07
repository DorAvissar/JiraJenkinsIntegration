pipeline {
    agent any // Any available agent

    stages {
        stage('Hello World') { // Stage for basic confirmation
            steps {
                sh 'echo "Hello World!"' // Basic step to ensure the pipeline starts
            }
        }

        stage('Get Merged Branch Name') { // Stage to get the branch name after the last merge
            steps {
                script { // Using a script block to execute Groovy code
                    // Get the commit history for the current branch
                    def gitCommits = sh(script: 'git log --pretty=format:%s -n 10', returnStdout: true).trim().split('\n')

                    // Find the last commit message that indicates a merge
                    def lastMergeCommit = gitCommits.find { commit ->
                        commit.contains('Merge ')
                    }

                    // Extract the branch name from the merge commit message
                    def mergedBranchName = 'No recent merges found'
                    if (lastMergeCommit) {
                        def matcher = lastMergeCommit =~ /Merge (.*) into/
                        if (matcher.find()) {
                            mergedBranchName = matcher.group(1).trim()
                        }
                    }

                    // Output the branch name
                    echo "Branch name merged into main: ${mergedBranchName}"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
        }
    }
}
