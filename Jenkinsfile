pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/my-app'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Pulling code from GitHub...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying app...'
                sh 'docker stop my-app || true'
                sh 'docker rm my-app || true'
                sh "docker run -d -p 3000:3000 --name my-app ${DOCKER_IMAGE}:latest"
            }
        }

    }

    post {
        success {
            echo 'Pipeline succeeded! App is live on port 3000.'
        }
        failure {
            echo 'Pipeline failed! Check the Console Output for details.'
        }
    }
}
