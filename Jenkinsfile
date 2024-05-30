pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "s5carles/let-do-it"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER} -f application-01.Dockerfile .'
                }
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-cred', variable: 'DOCKER_HUB_TOKEN')]) {
                    script {
                        sh 'echo ${DOCKER_HUB_TOKEN} | docker login -u s5carles --password-stdin'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Use double quotes to allow Jenkins to interpolate the variable
                    sh "docker run -d -P ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "docker ps"
                    
                }
            }
        }
    }
    // post {
    //     success {
    //         slackSend (
    //             channel: '#your-slack-channel',
    //             color: 'good',
    //             message: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) succeeded. Docker image: ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
    //         )
    //     }
    //     failure {
    //         slackSend (
    //             channel: '#your-slack-channel',
    //             color: 'danger',
    //             message: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) failed."
    //         )
    //     }
    // }
}
