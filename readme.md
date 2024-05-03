step1 - Deploy the following services: ! 
a. Jira server
b. Confluence server
c. Jenkins server 
by useing docker compose. [all services under the same network 'atlassian']
* added an sql database
* added environment to be able to access the services [keep in mind that the correct method is not to put passwords in the docker compose yml]
commend - "docker compose up -d" and verify that all containers in docker desktop are runing and can be access via the webserver. 

step 2 - 2. Create application link between Jira and Confluence
 jira - application links - create link 
 why? 
 1. With an application link, users can seamlessly navigate between Jira issues and Confluence pages
 2. With the integration, you can search across both Jira and Confluence. This is useful when you need to find related documentation or track down the context behind an issue or task.

step 3 - Connect Jenkins to a Git repository 
1. I downloaded the plugin - git plugin
2. i Created a Personal Access Token in github with the username and password of my github
2. In the settings of the job, I entered the URL of the git
3. i added the same Credentials in Jenkins ("Manage Jenkins" > "Manage Credentials.")
4. i created a Jenkins Job with GitHub Source
5. i have Set Up GitHub Webhooks which defines the trigers for the build in jenkins
to verify the connection , i make a merge between two branches to see that it is trigers the build process. 

step 4 - when a git branch is merged into master branch: Extract the Jira issue key from the branch name and Move the Jira issue to “Done”
1. It is required to make a link between the jenkins and the jira: manage jenkins - system - jirasite - validate settings 
* Don't forget to enter the username and password of jira as a variable in jenkins before
2. Now that there is an indication that the link was successful, write the Pipeline.
3. pipeline {
    agent any
    environment {
        JIRA_SITE = 'http://localhost:9090/' - להגדיר את הכתובת של הגירה
        JIRA_CREDS = credentials('jira_cred') - לקחת את המשתנה שהגדרנו בגנקינס לטובת הכניסה לגירה
    }

    stages { -בשלב הזה מתחברים לגיט לענף הראשי
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DorAvissar/MethodaProject.git/'
            }
        }

        stage('Extract Jira Issue Key') {
            when {
                changeset pattern: "refs/heads/(?:.*?/)?+([A-Z]+-\\d+)(?:\\/.*)?", comparator: "REGEXP"
            }
            steps {
                script {
                    def matcher = currentBuild.changeSets[0]?.items?.find { item -> item.msg =~ /Merge/ }?.msg
                    def issueKey = (matcher =~ /refs\/heads\/(?:.*?\/)?+([A-Z]+-\d+)/)[0][1]
                    if (issueKey) {
                        env.JIRA_ISSUE_KEY = issueKey
                        echo "Extracted Jira issue key: $issueKey"
                    } else {
                        error("No Jira issue key found in branch name")
                    }
                }
            }
        }

        stage('Move Jira Issue to Done') { - בשלב הזה הוא מעביר את המשימה בגירה לסטטוס הרצוי
            when {
                expression { env.JIRA_ISSUE_KEY != null }
            }
            steps {
                script {
                    def statusResponse = httpRequest consoleLogResponseBody: true, contentType: 'APPLICATION_JSON', httpMode: 'PUT',
                        requestBody: '{"fields":{"status":{"name":"Done"}}}',
                        url: "${JIRA_SITE}/rest/api/2/issue/${env.JIRA_ISSUE_KEY}",
                        validResponseCodes: '200',
                        authentication: JIRA_CREDS
                    if (statusResponse.status != 200) {
                        error "Failed to update Jira issue status. HTTP code: ${statusResponse.status}"
                    }
                }
            }
        }
    }
}
