pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'ecommerce-app'
        CONTAINER_REGISTRY = 'icr.io/<your-region>/<your-namespace>'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/GaganHK/IBM-Project.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE_NAME:latest ./frontend'
                    sh 'docker build -t $DOCKER_IMAGE_NAME-backend:latest ./backend'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh './frontend/tests.sh'
                    sh './backend/tests.sh'
                }
            }
        }
        stage('Push to IBM Cloud Container Registry') {
            steps {
                script {
                    sh 'docker login -u <your-ibm-cloud-username> -p <api-key> $CONTAINER_REGISTRY'
                    sh 'docker tag $DOCKER_IMAGE_NAME:latest $CONTAINER_REGISTRY/$DOCKER_IMAGE_NAME:latest'
                    sh 'docker push $CONTAINER_REGISTRY/$DOCKER_IMAGE_NAME:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl set image deployment/ecommerce-frontend ecommerce-frontend=$CONTAINER_REGISTRY/$DOCKER_IMAGE_NAME:latest'
                    sh 'kubectl set image deployment/ecommerce-backend ecommerce-backend=$CONTAINER_REGISTRY/$DOCKER_IMAGE_NAME-backend:latest'
                    sh 'kubectl rollout status deployment/ecommerce-frontend'
                    sh 'kubectl rollout status deployment/ecommerce-backend'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution complete.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
