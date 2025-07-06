pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/jnkartikx/flask-ci-cd.git'
        DOCKER_IMAGE = 'kartikj7/flask-app'
        DEPLOY_SERVER = 'ec2-user@16.16.209.95'
        DOCKER_USERNAME = credentials('docker-username') // Needs to be set in Jenkins Credentials
        DOCKER_PASSWORD = credentials('docker-password') // Needs to be set in Jenkins Credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t %DOCKER_IMAGE%:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    bat """
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                        docker push %DOCKER_IMAGE%:latest
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    sshagent(['deploy-key']) {
                        bat """
                            ssh -o StrictHostKeyChecking=no %DEPLOY_SERVER% "docker pull %DOCKER_IMAGE%:latest && docker run -d -p 5000:5000 %DOCKER_IMAGE%:latest"
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
    }
}
