pipeline {
    agent any
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Ensure Docker can run without sudo in your Jenkins environment
                    sh 'docker build -t shivangkataria/my-app-1.0 .'
                }
            }
        }
        stage('Push Docker Image') {
            steps{
                script {
                    withCredentials([string(credentialsId: 'shivangkataria', variable: 'shivangkataria')]) {
                        sh 'docker login -u shivangkataria -p ${shivangkataria}'
                        
                        sh 'docker push shivangkataria/my-app-1.0'
}
                }
            }
        }
        stage('Deploy on EC2') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'rndem', keyFileVariable: 'SSH_KEY')]) {
                    // Pull the Docker image on the EC2 instance
                    sh "ssh -i $SSH_KEY -o StrictHostKeyChecking=no ubuntu@ec2-100-26-46-100.compute-1.amazonaws.com 'docker pull shivangkataria/my-app-1.0'"

                    // Run the Docker container on the EC2 instance (modify command as needed)
                    sh "ssh -i $SSH_KEY -o StrictHostKeyChecking=no ubuntu@ec2-100-26-46-100.compute-1.amazonaws.com 'docker run -d -p 8000:8000 shivangkataria/my-app-1.0'"
            }
        }
    }
}
    }
}
