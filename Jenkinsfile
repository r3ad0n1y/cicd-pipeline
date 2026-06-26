pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        // Здесь ваше настоящее имя и новый репозиторий (с дефисами)
        DOCKER_IMAGE = 'sserebrennykov/cicd-jenkins-task' 
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }

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

        // Переписали этот шаг на чистый sh в обход плагина
        stage('Docker Image Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        // Переписали шаг отправки с использованием credentials
        stage('Docker Image Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    // Логинимся в Docker Hub по токену, тегируем и пушим
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }
}
