pipeline {
    agent any
    parameters {
        string(name: 'MET-1', defaultValue: 'JIR-123', description: 'JIRA Issue Key to check if it exists')
    }
    stages {
        stage('Check JIRA Issue') {
            steps {
                script {
                    // Placeholder for your JIRA issue key
                    def issueKey = params.JIRA_ISSUE_KEY
                    boolean issueExists = checkJiraIssueExists(issueKey)
                    if (issueExists) {
                        echo "JIRA issue ${issueKey} exists."
                    } else {
                        echo "JIRA issue ${issueKey} does not exist."
                    }
                }
            }
        }
    }
}

def checkJiraIssueExists(String issueKey) {
    try {
        def jiraIssue = jiraGetIssue(idOrKey: issueKey)
        return jiraIssue != null
    } catch (Exception e) {
        return false
    }
}
