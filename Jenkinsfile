pipeline {
    agent any

    environment {
        IMAGE_NAME = "nguyenbakhanh/simple-cicd-app"
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/KhanhNb2k/simple-cicd-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        docker.image("${IMAGE_NAME}:${BUILD_NUMBER}").push()
                        docker.image("${IMAGE_NAME}:${BUILD_NUMBER}").push('latest')
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                docker pull ${IMAGE_NAME}:${BUILD_NUMBER}
                docker stop simple-cicd-app || true
                docker rm simple-cicd-app || true
                docker run -d \
                  --name simple-cicd-app \
                  -p 3000:3000 \
                  ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }
}
