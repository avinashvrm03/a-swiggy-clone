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
                 withSonarQubeEnv('SonarQube-Server') {
                     sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=a-swiggy-clone \
                     -Dsonar.projectKey=a-swiggy-clone '''
                 }
        }
      }
    }
    stage('Quality Gate') {
      steps {
        script {
      waitForQualityGate abortPipeline: false; credentialsId: 'sonarqube-token'
        }
    }
    }
    stage('Install Dependencies') {
      steps {
        script {
          sh 'npm install'
        }
      }
    }
    stage('TRIVY FS SCAN') {
      steps {
        script {
          sh 'trivy fs . > trivyfs.txt'
        }
      }
    }
  }
}
