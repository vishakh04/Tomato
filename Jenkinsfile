pipeline {
    agent any

    environment {
        IMAGE_NAME = "reactapp"
        DOCKER_HUB_USER = "vishakhsingh7"
        RANDOM_PORT = "5173"
        CONTAINER_NAME = "${IMAGE_NAME}-${BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repo') {
            steps {
                retry(2) {
                    bat 'git config --global http.sslVerify false'
                    git branch: 'main', url: 'https://github.com/vishakh04/Tomato.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                }
                bat '''
                    set DOCKER_BUILDKIT=1
                    docker build -t %IMAGE_NAME% .
                '''
            }
        }

        stage('Stop Previous Container') {
            steps {
                script {
                    echo "Stopping previous container if exists..."
                }
                bat '''
                    docker stop %CONTAINER_NAME% || exit 0
                    docker rm %CONTAINER_NAME% || exit 0
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo "Running Docker container..."
                }
                bat '''
                    docker run -d -p %RANDOM_PORT%:80 --name %CONTAINER_NAME% %IMAGE_NAME%
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubcred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    script {
                        echo "Pushing image to Docker Hub..."
                    }
                    bat '''
                        echo %PASS% | docker login -u %USER% --password-stdin
                        docker tag %IMAGE_NAME% %DOCKER_HUB_USER%/%IMAGE_NAME%:latest
                        docker push %DOCKER_HUB_USER%/%IMAGE_NAME%:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "Cleaning up..."
            }
            bat '''
                docker logout
                docker system prune -f --volumes
            '''
        }
        success {
            script {
                echo "Pipeline completed successfully!"
            }
        }
        failure {
            script {
                echo "Pipeline failed!"
            }
        }
    }
}
