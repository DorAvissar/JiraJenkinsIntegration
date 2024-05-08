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

        stage('Add Comment to Jira') {
            steps {
                script {
                    def issueKey = env.JIRA_ISSUE_KEY
                    if (issueKey) {
                        withEnv(["JIRA_SITE=${JIRA_SITE_NAME}"]) {
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
                script {
                    def issueKey = env.JIRA_ISSUE_KEY

                    if (issueKey) {
                        withEnv(["JIRA_SITE=${JIRA_SITE_NAME}", "JIRA_URL=${JIRA_BASE_URL}"]) {
                            jiraTransitionIssue(
                                issueSelector: [issueKey: issueKey], 
                                transition: '31' // Transition ID
                            )
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
