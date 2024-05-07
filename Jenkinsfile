pipeline {
  agent any
  stages {
    stage('Hello World') {
      steps {
        sh 'echo "Hello World!"'
      }
    }

    stage('command') {
      steps {
        jiraTransitionIssue(issueKey: 'MET-3', transitionId: '31')
      }
    }

  }
  post {
    always {
      echo 'Pipeline completed.'
    }

  }
}
