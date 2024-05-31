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
                sh '''
                docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} -f application-01.Dockerfile 
                '''
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'carles-docker-hub', variable: 'DOCKER_HUB_TOKEN')]) {
                    sh '''
                    echo ${DOCKER_HUB_TOKEN} | docker login -u s5carles --password-stdin
                    '''
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}").push()
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                sh '''
                docker run -d -P ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}
                docker ps
                '''
            }
        }
    }
    post {
        success {
            slackSend (
                channel: 'jenkins-notification-ars',
                color: 'good',
                message: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) succeeded. Docker image: ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
            )
            emailext (
                subject: "Jenkins Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' Succeeded",
                body: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) succeeded. Docker image: ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]
            )
        }
        failure {
            slackSend (
                channel: 'jenkins-notification-ars',
                color: 'danger',
                message: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) failed."
            )
            emailext (
                subject: "Jenkins Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed",
                body: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) failed.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]
            )
        }
    }
}
