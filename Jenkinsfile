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
                      userRemoteConfigs: [[url: 'https://github.com/beglaryanzhan/testingTest']]
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
            def commit = sh(returnStdout: true, script: 'git log -1 --pretty=%B | awk '{ print $1 }' | xargs')
            def Issuekey = (commit =~ '([A-Z][A-Z0-9]+)')
          }
        } 
      }      
    }
  post {
    success {
      script {
        if (Issuekey)
          def messaging = [ body: 'Build Status: Success, Branch: ${env.GIT_BRANCH}' ]
          jiraAddComment site: 'JIRA', idOrKey: Issuekey, input: messaging
      }
    }
  }
}
