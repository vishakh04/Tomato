pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'vishakhsingh7/reactapp2:latest'
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials') // Make sure to configure Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                // Pull the latest code from the GitHub repository
                git 'https://github.com/vishakh04/Tomato.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Log into Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
    }

    post {
        success {
            echo 'Docker image pushed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
