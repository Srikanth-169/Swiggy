pipeline {
  agent any

  environment {
    DOCKERHUB_CRED = 'srikanth169-dockerhub'     // Jenkins credentials ID
    DOCKERHUB_USER = 'srikanth169'  // Docker Hub username
    IMAGE_NAME = "${DOCKERHUB_USER}/swiggy"
    TAG = "v1"
    HOST_PORT = "8084"
    CONTAINER_PORT = "80"
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/Srikanth-169/Swiggy.git', branch: 'master'
      }
    }

    stage('Build & Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: env.DOCKERHUB_CRED, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker build -t ${IMAGE_NAME}:${TAG} .
            docker push ${IMAGE_NAME}:${TAG}
          '''
        }
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          docker rm -f swiggy || true
          docker run -d --name swiggy -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:${TAG}
        '''
      }
    }
  }

  post {
    always {
      sh 'docker logout || true'
      cleanWs()
    }
  }
}

