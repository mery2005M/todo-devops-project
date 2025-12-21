pipeline {
    agent any

    environment {
        DOCKER_API_VERSION = '1.43'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME = 'mery2005'
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/todo-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/todo-frontend"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Pulling code from GitHub...'
                git branch: 'main',
                    url: 'https://github.com/mery2005M/todo-devops-project.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                echo 'Building Backend Docker image...'
                dir('backend') {
                    sh 'docker build -t ${BACKEND_IMAGE}:latest -t ${BACKEND_IMAGE}:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo 'Building Frontend Docker image...'
                dir('frontend') {
                    sh 'docker build -t ${FRONTEND_IMAGE}:latest -t ${FRONTEND_IMAGE}:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo 'Logging into Docker Hub...'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                echo 'Pushing images to Docker Hub...'
                sh '''
                docker push ${BACKEND_IMAGE}:latest
                docker push ${BACKEND_IMAGE}:${BUILD_NUMBER}
                docker push ${FRONTEND_IMAGE}:latest
                docker push ${FRONTEND_IMAGE}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Cleanup Local Images') {
            steps {
                echo 'Cleaning up local Docker images...'
                sh '''
                docker rmi ${BACKEND_IMAGE}:${BUILD_NUMBER} || true
                docker rmi ${FRONTEND_IMAGE}:${BUILD_NUMBER} || true
                '''
            }
        }
    }

    post {
        always {
            echo 'Logging out from Docker Hub...'
            sh 'docker logout || true'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check logs.'
        }
    }
}
