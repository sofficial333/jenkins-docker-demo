pipeline {

    agent any

    environment {
        DOCKER_IMAGE = 'soffical333/jenkins-docker-demo'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading code from GitHub...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t ${DOCKER_IMAGE}:latest .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo 'Logging in to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'

                sh '''
                    docker push ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Run Container') {
            steps {
                echo 'Starting Docker container...'

                sh '''
                    docker stop jenkins-demo || true
                    docker rm jenkins-demo || true

                    docker run -d \
                        --name jenkins-demo \
                        -p 8081:80 \
                        ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }

    post {

        success {
            echo '======================================'
            echo 'Pipeline completed successfully!'
            echo 'Website: http://YOUR_SERVER_IP:8081'
            echo '======================================'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}