**the task - Jenkins and Git Integration:** 
1. Deploy the following services:
   a. Jira server
   b. Jenkins server
Use your chosen configuration and infrastructure as code utility (Terraform/Chef/Kubernetes/Docker compose/ ... ) I chose to use docker compose
2. Connect Jenkins to a Git repository
3. Create a Job in Jenkins, and a pipeline that will:
   a. when a git branch is merged into master branch:
   b. Extract the Jira issue key from the branch name (Issue key is of the form: EX-123, means string of capital letters, followed by a
   hyphen, followed by an integer)
   c. Move the Jira issue to “Done”

### So, lets solve it: 

1. **Creating docker-compose file with jira and jenkins**
- I used Docker Compose to run all environments locally, aiming for convenience and efficiency, you can check out the file in the repository. 

2. **Services Verification and User Configuration**
   After setting up the Docker Compose, I verified that all environments were running properly, with each container accessible. Additionally, I logged into each site to create and configure user accounts as instructed.
   note that the  initial Jenkins admin password is located in "/var/jenkins_home/secrets/initialAdminPassword" 

3. **Jira and Jenkins Integration**
   - Download the Jira plugin in Jenkins (jira integration, jira issue updater, jira plugin, jira pipeline steps)
   - Configure Jira authentication in Jenkins using a username and password.
   - Create a Jira site and verify the connection.

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jirasite.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jirasite3.png?raw=true">

4. **Jenkins and Git Integration**
   To establish a connection between Jenkins and GitHub, you need to create a webhook in GitHub that communicates with Jenkins. Follow these steps to configure it properly:
   On GitHub:
   - Payload URL: Configure this to point to your Jenkins URL with the suffix /github-webhook/. This endpoint acts as a bridge for GitHub to inform Jenkins about repository events.
   - Note: Using localhost URLs won't cut it. You'll need a tool like ngrok to securely expose your Jenkins URL to GitHub.
   - Content Type: Ensure that it's set to application/json. This specification defines how data is transmitted to Jenkins.
   - Which Events to Trigger: Select "Pull Requests". This choice ensures that Jenkins receives notifications whenever a pull request is opened or updated in your repository.

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/githubwebhook1.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/githubwebhook2.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/githubwebhook.png?raw=true">

   
   On Jenkins:
   - Install GitHub Integration Plugin: Begin by downloading and installing the GitHub Integration plugin in Jenkins. This plugin facilitates seamless communication between Jenkins and GitHub.
   - Generate GitHub Token: Create a token on GitHub, granting Jenkins the necessary permissions. Then, configure this token in Jenkins under "Manage Jenkins."
   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/gittoken.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/gittoken2.png?raw=true">

   - Configure Job Settings: In your Jenkins job settings, define the GitHub project using your repository's URL. Additionally, set up Build Triggers to include "GitHub Pull Requests" with the desired trigger event (e.g., PR closed), along with "GitHub hook trigger for GITScm polling."
   - Define Pipeline: Within the pipeline section, specify the path to your Jenkinsfile. This file serves as the blueprint for your Jenkins builds, defining the steps and actions to be executed.
By implementing these configurations, you enable smooth communication between Jenkins and GitHub. This integration empowers Jenkins to automatically trigger builds and execute predefined actions in response to events occurring within your GitHub repository.

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jenkinsconfig1.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jenkinsconfig2.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jenkinsconfig3.png?raw=true">

   <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/jenkinsconfig4.png?raw=true">


5. **Jenkins File**
   As previously explained, Jenkins relies on the Jenkinsfile stored in your Git repository to execute the build process. To accomplish your specific task of updating Jira issues to "Done" based on branch names, you'll need to craft the Jenkinsfile accordingly.
   The Jenkinsfile should be designed to retrieve the branch name and then verify its existence as an issue in Jira. If the branch corresponds to an existing issue, the Jenkinsfile should incorporate plugin functions to transition the issue status to "Done."
   During this process, I consulted various <a href="https://jenkinsci.github.io/jira-steps-plugin/getting-started/"> documentation resources</a> to ensure accurate implementation. Additionally, you're welcome to examine the Jenkinsfile within my repository for further insights into the implementation details.

Hope you enjoy the project ! 

 <img src="https://github.com/DorAvissar/JiraJenkinsIntegration/blob/main/assets/finalstep.png?raw=true">





