pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/USERNAME/REPO.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t USERNAME/myweb:latest .'
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'docker login -u $USER -p $PASS'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push USERNAME/myweb:latest'
            }
        }

        stage('Deploy on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@EC2_PUBLIC_IP << EOF
                      docker stop web || true
                      docker rm web || true
                      docker pull USERNAME/myweb:latest
                      docker run -d -p 80:80 --name web USERNAME/myweb:latest
                    EOF
                    '''
                }
            }
        }
    }
}
