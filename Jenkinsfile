pipeline {
  agent { label "Jenkins-Agent" }
  tools {
    jdk 'java21'
    nodejs 'node16'    
  }
  environment{
    SCANNER_HOME=tool 'SonarQube-Server'
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
              sh "${scannerHome}/bin/SonarQube-Server"
                }
        }
      }
    }
  }
}
