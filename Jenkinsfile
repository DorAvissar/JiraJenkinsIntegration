pipeline {
    agent any

    steps {
         stage('Hello World') {
            steps {
                sh 'echo "Hello World!"' // Basic stage to confirm pipeline starts
            }
        }
        script {
            // Get the commit history for the main branch
            def mainBranchCommits = git changelog(branch: 'main')

            // Find the last commit message that mentions a merge
            def lastMergeCommit = mainBranchCommits.find { commit ->
                commit.msg =~ /Merge (.*) into main/
            }

            // Extract the branch name from the merge commit message (if found)
            def mergedBranchName = lastMergeCommit?.msg =~ /Merge (.*) into main/ ? (captures()[0]) : 'No recent merges found'

            // Print the branch name
            echo "Branch name merged to main: ${mergedBranchName}"
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

