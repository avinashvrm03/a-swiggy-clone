pipeline {
  agent { label "Jenkins-Agent"}
  tools {
    jdk 'java21'
    nodejs 'node16'
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
          docker.withRegistry('', 'dockerhub') {
            docker_image = docker.build"${IMAGE_NAME}"
          }
          docker.withRegistry('', 'dockerhub') {
            docker_image.push"${IMAGE_TAG}"
          }
        }
      }
    }
    stage('Trivy Scan') {
      steps {
        script {
          sh 'docker run -v /var/run/docker.sock:var/run/docker.sock aquasec/trivy image avinash0001/a-swiggy-clone:latest'
        }
      }
    }
  }
}
