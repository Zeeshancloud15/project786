pipeline {
    agent any

    environment {
        IMAGE_NAME = "zeeshancloud15/pythonapp"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        success {
            slackSend(
                tokenCredentialId: 'slack-token',
                channel: '#jenkins',
                color: 'good',
                message: "✅ SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}"
            )
        }

        failure {
            slackSend(
                tokenCredentialId: 'slack-token',
                channel: '#jenkins',
                color: 'danger',
                message: "❌ FAILED: ${JOB_NAME} #${BUILD_NUMBER}"
            )
        }

        always {
            sh 'docker logout || true'
        }
    }
}
