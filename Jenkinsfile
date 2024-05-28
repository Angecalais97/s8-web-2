pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('carles-docker-hub')
        // SLACK_CREDENTIALS = credentials('slack-credentials-id')
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
                    docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}", "-f application-01.Dockefile .")
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'carles-docker-hub') {
                        // login is handled by withRegistry
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
    }
    // post {
    //     success {
    //         slackSend (
    //             channel: '#your-slack-channel',
    //             color: 'good',
    //             message: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) succeeded."
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
