pipeline {
    agent any
    
    environment {
        // Define a dynamic tag for the Docker image
        // For example, using the Jenkins build number
        DOCKER_IMAGE_TAG = "shivangkataria/my-app-${BUILD_NUMBER}"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'shivangkataria', variable: 'shivangkataria')]) {
                        sh "docker login -u shivangkataria -p ${shivangkataria}"
                        sh "docker push ${DOCKER_IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy on EC2') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'rndem', keyFileVariable: 'SSH_KEY')]) {
                        // Pull the Docker image on the EC2 instance
                        sh "ssh -i $SSH_KEY -o StrictHostKeyChecking=no ubuntu@ec2-100-26-46-100.compute-1.amazonaws.com 'docker pull ${DOCKER_IMAGE_TAG}'"

                        // Run the Docker container on the EC2 instance
                        sh "ssh -i $SSH_KEY -o StrictHostKeyChecking=no ubuntu@ec2-100-26-46-100.compute-1.amazonaws.com 'docker run -d -p 8000:8000 ${DOCKER_IMAGE_TAG}'"
                    }
                }
            }
        }
    }
}
