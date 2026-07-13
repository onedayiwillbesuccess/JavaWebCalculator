pipeline {

    agent any

    environment {
        IMAGE_NAME = "javawebcalculator"
        CONTAINER_NAME = "javawebcalculator"
        HOST_PORT = "8081"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/onedayiwillbesuccess/JavaWebCalculator.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh '''
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${HOST_PORT}:8080 \
                    ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

    }

    post {

        success {
            echo "Deployment Successful"
            echo "Application URL:"
            echo "http://YOUR_SERVER_IP:${HOST_PORT}"
        }

        failure {
            echo "Deployment Failed"
        }

    }
}
