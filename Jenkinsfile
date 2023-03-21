def commit
def Issuekey

def agentLabel
def ENV_FILE
def DOCKER_FILE
if (BRANCH_NAME == "main") {
    agentLabel = "main"
    DOCKER_FILE = "docker-compose.stage.yml"
    ENV_FILE = "stage.env"
} else {
    agentLabel = "master" 
    DOCKER_FILE = "docker-compose.prod.yml"
    ENV_FILE = "prod.env"
}
pipeline {
    agent any
    stages {
      stage('Checkout') {
        steps {
          checkout([$class: 'GitSCM', 
                      branches: scm.branches,
                      extensions: [[$class: 'CleanCheckout']],
                      userRemoteConfigs: [[url: 'https://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@github.com/beglaryanzhan/app1']]
          ])
        }
      }        
      stage('testing env') {
        steps {
            echo "$DOCKER_FILE"
            echo "$agentLabel"
        }
      }
      stage('Jira') {
        steps {
          script {
            commit = sh(returnStdout: true, script: 'git log -1 --pretty=%B | awk \'{ print $1 }\' | xargs')
            Issuekey = (commit ==~ '([A-Z]{2,}-[0-9]+)')
            if (Issuekey){
              def messaging = [ body: 'Build Status Success, Branch ${env.GIT_BRANCH}' ]
              jiraComment site: 'JIRA', IssueKey: commit, input: messaging
           }
          }
        } 
      }      
    }
} 
