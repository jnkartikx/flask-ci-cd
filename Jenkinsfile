pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/jnkartikx/flask-ci-cd.git'
        DOCKER_IMAGE = 'kartikj7/rrequero/flask-app'
        DEPLOY_SERVER = 'ec2-user@<16.16.209.95>'
    }

   stage('Clone Repository') {
    steps {
        git 'https://github.com/jnkartikx/flask-ci-cd.git'
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
