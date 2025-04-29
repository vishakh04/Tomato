pipeline {
  agent any

  environment {
    IMAGE_NAME = "reactapp"
    DOCKER_HUB_USER = "vishakhsingh7"
    RANDOM_PORT = "8082"
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
        bat '''
          set DOCKER_BUILDKIT=1
          docker build -t %IMAGE_NAME% .
        '''
      }
    }

    stage('Stop Previous Container') {
      steps {
        bat '''
          FOR /F "tokens=1" %%i IN ('docker ps -q --filter "publish=8082"') DO docker stop %%i
          FOR /F "tokens=1" %%i IN ('docker ps -a -q --filter "publish=8082"') DO docker rm %%i
        '''
      }
    }

    stage('Run Docker Container') {
      steps {
        bat '''
          docker run -d -p %RANDOM_PORT%:80 --name %CONTAINER_NAME% %IMAGE_NAME%
        '''
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhubcred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
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
      bat '''
        docker logout
        docker system prune -f --volumes
      '''
    }
  }
}
