pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_URL = '' // Use 'https://index.docker.io/v1/' for Docker Hub if needed
        DOCKER_IMAGE = "shivangkataria/my-app"
        DOCKER_TAG = "1.0-${BUILD_NUMBER}"
        IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
        EC2_HOST = "ubuntu@ec2-100-26-46-100.compute-1.amazonaws.com" // Replace with your EC2 instance address
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(IMAGE)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry(DOCKER_REGISTRY_URL, 'shivangkataria') {
                        docker.image(IMAGE).push()
                    }
                }
            }
        }

        stage('Deploy on EC2') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'rndem', keyFileVariable: 'SSH_KEY')]) {
                        sh "ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${EC2_HOST} 'docker pull $IMAGE && docker run -d -p 8000:8000 $IMAGE'"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean up workspace after build completion
        }
    }
}
