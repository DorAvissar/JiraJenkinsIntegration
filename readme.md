step1 - Deploy the following services:
a. Jira server
b. Confluence server
c. Jenkins server 
by useing docker compose. [all services under the same network 'atlassian']
* added an sql database
* added environment to be able to access the services [keep in mind that the correct method is not to put passwords in the docker compose yml]
commend - "docker compose up -d" and verify all containers in docker desktop and via the webserver. 

step 2 - 2. Create application link between Jira and Confluence
 jira - application links - create link 
 why? 
 1. With an application link, users can seamlessly navigate between Jira issues and Confluence pages
 2. With the integration, you can search across both Jira and Confluence. This is useful when you need to find related documentation or track down the context behind an issue or task.
 3. hkjnkj