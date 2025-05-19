pipeline {
  agent { label "Jenkins-Agent" }
  tools {
    jdk 'jdk21'
    nodejs 'node16' 
  }
  environment{
    SCANNER_HOME=tool 'sonarqube-scanner'
    APP= 'swiggy-clone'
    RELEASE= '1.0.0'
    DOCKER_USER= 'avinash0001'
    DOCKER_PASS= 'dockerhub'
    IMAGE_NAME='${DOCKER_USER}/${APP}'
    IMAGE_TAG='${RELEASE}-${BUILD_NUMBER}'
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
    stage('Build And Push') {
      steps {
        script {
          docker.withRegitry('', DOCKER_PASS) {
            docker_image = docker.build"${IMAGE_NAME}"            
          }
          docker.withRegistry('', DOCKER_PASS) {
            docker_image.push"${IMAGE_TAG}"
            docker_image.push('latest')
          }
        }
      }
    }
  }
}
