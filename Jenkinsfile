pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/jnkartikx/flask-ci-cd.git'
        DOCKER_IMAGE = 'kartikj7/flask-ci-cd:latest'
          DEPLOY_SERVER = 'ubuntu@16.170.98.45'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',credentialsId: 'dockerhub', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t ${DOCKER_IMAGE} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                    docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%
                    docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([file(credentialsId: 'deploy-key', variable: 'KEY')]) {
                    bat """
                    plink.exe -i %KEY% -batch ubuntu@16.170.98.45 "docker pull ${DOCKER_IMAGE} && docker stop flask-container || exit 0 && docker rm flask-container || exit 0 && docker run -d -p 5000:5000 --name flask-container ${DOCKER_IMAGE}"
                    """
                }
            }
        }
    }
}
