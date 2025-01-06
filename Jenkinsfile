pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Application Build') {
            steps {
                sh 'scripts/build.sh'
            }
        }

        stage('Tests') {
            steps {
                sh 'scripts/test.sh'
            }
        }

        stage('Docker Image Build') {
            steps {
                script {
                    sh "docker build -t your-dockerhub-username/mybuildimage:${env.BUILD_NUMBER} ."
                    sh "docker tag your-dockerhub-username/mybuildimage:${env.BUILD_NUMBER} your-dockerhub-username/mybuildimage:latest"
                }
            }
        }

        stage('Docker Image Push') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker_hub_creds_id', 
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )]) {
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                        sh "docker push your-dockerhub-username/mybuildimage:${env.BUILD_NUMBER}"
                        sh "docker push your-dockerhub-username/mybuildimage:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            sh "docker logout"
        }
    }
}
