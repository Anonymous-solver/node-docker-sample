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
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Push to Harbor') {
            steps {
                script {
                    def harborHost = 'ec2-13-214-133-38.ap-southeast-1.compute.amazonaws.com:80'
                    def harborProject = 'anik-demo'
                    def harborUser = 'admin'
                    def harborPassword = 'Harbor12345'
 
                    // Tag image for Harbor
                    sh "docker tag ${DOCKER_IMAGE}:latest ${harborHost}/${harborProject}/node-demo-80:latest"
 
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
                    docker run -d -p 3000:3000 --name node-app ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }
}
