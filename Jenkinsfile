pipeline {
  agent any
  stages {
    stage('Git Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Application Build') {
      steps {
        sh '''
                    chmod +x scripts/build.sh
                    ./scripts/build.sh
                '''
      }
    }

    stage('Test') {
      steps {
        sh '''
                    chmod +x scripts/test.sh
                    ./scripts/test.sh
                '''
      }
    }

    stage('Docker Image Build') {
      steps {
        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
      }
    }

    stage('Docker Image Push') {
      steps {
        withCredentials(bindings: [usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
          sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
          sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
          sh "docker push ${DOCKER_IMAGE}:latest"
        }

      }
    }

  }
  environment {
    DOCKER_IMAGE = 'sserebrennykov/cicd-jenkins-task'
    DOCKER_TAG = "${env.BUILD_NUMBER}"
  }
  options {
    skipDefaultCheckout(true)
  }
}