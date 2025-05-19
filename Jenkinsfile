pipeline {
  agent { label "Jenkins-Agent" }
  tools {
    jdk 'jdk21'
    nodejs 'node16' 
  }
  environment{
    SCANNER_HOME=tool 'sonarqube-scanner'
  }
  stages {
    stage('Clean WorkSpace') {
      steps {
        cleanWs()
      }
    }
    stage('Checkout SCM') {
      steps {
        git branch: 'main', credentialsId: 'github', url: 'https://github.com/avinashvrm03/a-swiggy-clone.git'
      }
    }
    stage('Sonarqube Analysis') {
      steps {
        script {
            withSonarQubeEnv() {
              sh "${SCANNER_HOME}/bin/sonarqube-scanner"
                }
        }
      }
    }
  }
}
