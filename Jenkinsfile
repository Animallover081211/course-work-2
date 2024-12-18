pipeline {
    agent any

    environment {
        IMAGE_NAME = 'fudukhomoh/cw2-server'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout from github') {
            steps {
                
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Test Docker Container') {
            steps {
                script {
                    sh "docker run -d --name test-container ${IMAGE_NAME}:${IMAGE_TAG}"

                    sh "docker ps | grep test-container"

                    sh "docker stop test-container"
                    sh "docker rm test-container"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }

                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
        }
    }
}

