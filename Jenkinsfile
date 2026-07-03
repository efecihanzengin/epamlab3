pipeline {
    agent any
    tools {
        nodejs 'Node7'
    }
    environment {
        IMAGE_TAG = "${env.BRANCH_NAME == 'release' ? 'widgetrelease:v1.0' : 'widgetstaging:v1.0'}"
        HOST_PORT = "${env.BRANCH_NAME == 'release' ? '5000' : '5001'}"
        CONTAINER_NAME = "${env.BRANCH_NAME == 'release' ? 'widget-release' : 'widget-staging'}"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_TAG} ."
            }
        }
        stage('Deploy') {
            steps {
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} --expose ${HOST_PORT} -p ${HOST_PORT}:5000 ${IMAGE_TAG}
                """
            }
        }
    }
}
