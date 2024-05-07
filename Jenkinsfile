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
        jiraComment(issueKey: 'MET-3', body: 'from jenkins 2.0')
      }
    }

  }
  post {
    always {
      echo 'Pipeline completed.'
    }

  }
}
