pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('creds')
        IMAGE_NAME = 'zeeshancloud15/pythonapp'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Zeeshancloud15/project786.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh '''
                    echo "$DOCKERHUB_CREDENTIALS_PSW" |
                    docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }

        success {
            slackSend message: "Build successful - ${env.JOB_NAME} ${env.BUILD_NUMBER} (${env.BUILD_URL})"
        }

        failure {
            slackSend message: "Build failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (${env.BUILD_URL})"
        }
    }
}
