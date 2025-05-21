pipeline {
  agent { label "Jenkins-Agent"}
  tools {
    jdk 'java21'
  }
  environment {
    SONAR_HOME =  tool 'Sonarqube-Scanner';
    APP = "a-swiggy-clone"
    DOCKER_USER = "avinash0001"
    DOCKER_PASS = "dockerhub"
    IMAGE_NAME = "${DOCKER_USER}/${APP}"
    IMAGE_TAG = "latest"
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
            sh """ ${SONAR_HOME}/bin/sonar-scanner -Dsonar.projectName=a-swiggy-clone \
            -Dsonar.projectKey=a-swiggy-clone """
          }
        }
      }
    }
    stage('Quality Gate') {
      steps {
        script {
        waitForQualityGate abortPipeline: false; credentialsId: 'Jenkins-Sonarqube_Token'
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
    stage('Build And Push to Dockerhub') {
      steps {
        script {
          docker.withRegistry('', 'DOCKER_PASS') {
            docker_image = docker.build"${IMAGE_NAME}"
          }
          docker.withRegistry('', 'DOCKER_PASS') {
            docker_image.push"$(IMAGE_TAG)"
          }
        }
      }
    }
  }
}
