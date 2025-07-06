pipeline {
  agent any
  environment {
    REPO_URL = 'https://github.com/jnkartikx/flask-ci-cd.git'
    DOCKER_IMAGE = 'kartikj7/flask-app'
    DEPLOY_SERVER = 'ubuntu@16.170.98.45'
  }
  stages {
    stage('Clone Repository') {
      steps {
        git REPO_URL
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          sh 'docker build -t ${DOCKER_IMAGE}:latest .'
        }
      }
    }
    stage('Push Docker Image') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh '''
              echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin
              docker push ${DOCKER_IMAGE}:latest
            '''
          }
        }
      }
    }
    stage('Deploy to EC2') {
      steps {
        script {
          sshagent(['deploy-key']) {
            sh '''
              ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} '
              docker pull ${DOCKER_IMAGE}:latest &&
              docker rm -f flask-app || true &&
              docker run -d --name flask-app -p 5000:5000 ${DOCKER_IMAGE}:latest
              '
            '''
          }
        }
      }
    }
  }
}
