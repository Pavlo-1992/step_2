pipeline {
  agent { label 'jenkins-agent' }

  environment {
    DOCKER_CREDENTIALS = 'dockerhub-credentials'
    IMAGE_NAME = 'savanchukpavlo/step_2'
    IMAGE_TAG = "${IMAGE_NAME}:${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/Pavlo-1992/step_2.git', branch: 'main'
      }
    }

    stage('Build Docker image') {
      steps {
          script {
            sh 'ls -la'
            docker.build("${IMAGE_TAG}")
        }
      }
    }

stage('Run tests') {
  steps {
    script {
        try {
          sh "docker run --rm --name step2_test_container ${IMAGE_TAG} test"
        } catch (err) {
          echo 'Test failed!'
          error("Stopping the pipeline!")
        }
      }
  }
}
    stage('Push to Docker Hub and clean up') {
      when {
        expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
      }
      steps {
        withCredentials([usernamePassword(
          credentialsId: "${env.DOCKER_CREDENTIALS}",
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          script {
            sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
            sh "docker tag ${IMAGE_TAG} ${IMAGE_NAME}:latest"
            sh "docker push ${IMAGE_TAG}"
            sh "docker push ${IMAGE_NAME}:latest"
            sh "docker rmi ${IMAGE_TAG} ${IMAGE_NAME}:latest"
          }
        }
      }
    }
  }
}

