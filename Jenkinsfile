pipeline {
  agent any
  stages {
    stage('add a comment to issue') {
      steps {
        jiraComment(issueKey: 'MET-1', body: 'from jenkkins')
      }
    }

  }
}