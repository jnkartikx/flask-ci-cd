pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/jnkartikx/flask-ci-cd.git'    // your repo URL
        DOCKER_IMAGE = 'kartikj7/flask-app'                          // your Docker Hub repo/image name
        DEPLOY_SERVER = 'ec2-user@16.170.98.45'                      // your EC2 user and IP
        DOCKER_USERNAME = 'kartikj7'                                 // your Docker Hub username
        DOCKER_PASSWORD = '<Kartik@jadon1234>'       // your Docker Hub password or access token
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
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    sshagent(['deploy-key']) {
                        sh '''
                            ssh ${DEPLOY_SERVER} "docker pull ${DOCKER_IMAGE}:latest && docker run -d -p 5000:5000 ${DOCKER_IMAGE}:latest"
                        '''
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
