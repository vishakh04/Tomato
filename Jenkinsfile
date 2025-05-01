pipeline {
    agent any

    environment {
        CHOKIDAR_USEPOLLING = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker-compose down'
                    sh 'docker-compose build'
                }
            }
        }

        stage('Run Containers') {
            steps {
                script {
                    sh 'docker-compose up -d'
                }
            }
        }

        stage('Test React App') {
            steps {
                script {
                    sh '''
                        sleep 10
                        curl --retry 10 --retry-delay 5 --retry-connrefused http://localhost:5173
                    '''
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh 'docker-compose down'
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
