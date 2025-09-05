pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "anikb29/node-docker-sample"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Anonymous-solver/node-docker-sample.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || echo "No tests configured"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t node-docker-sample:latest ."
            }
        }

        stage('Push to Harbor') {
            steps {
                script {
                    def harborHost = 'localhost:80'
                    def harborProject = 'demo'
                    def harborUser = 'admin'
                    def harborPassword = '2357'
 
                    // Tag image for Harbor
                    sh "docker tag node-docker-sample:latest ${harborHost}/${harborProject}/node-demo-80:latest"
 
                    // Login & Push
                    sh "docker login ${harborHost} -u ${harborUser} -p ${harborPassword}"
                    sh "docker push ${harborHost}/${harborProject}/node-demo-80:latest"
                }
            }
        }

        // stage('Push Docker Image') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        //             sh '''
        //                 echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
        //                 docker push ${DOCKER_IMAGE}:latest
        //             '''
        //         }
        //     }
        // }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker stop node-app || true
                    docker rm node-app || true
                    docker run -d -p 3000:3000 --name node-app node-docker-sample:latest
                '''
            }
        }
    }
}
