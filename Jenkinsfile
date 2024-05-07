pipeline {
    agent any

    stages {
        stage('Hello World') {
            steps {
                sh 'echo "Hello World!"'
            }
        }

        stage('Update Jira Issue') {
            steps {
                jiraComment(issueKey: 'MET-3', body: 'from jenkins')
                jiraTransitionIssue(issueKey: 'MET-3', transition: 'Done')
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
