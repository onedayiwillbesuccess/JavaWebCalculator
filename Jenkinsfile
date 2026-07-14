pipeline {

    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        IMAGE_NAME = "javawebcalculator"
        CONTAINER_NAME = "javawebcalculator"
        HOST_PORT = "15"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/onedayiwillbesuccess/JavaWebCalculator.git'
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    echo "Java Version"
                    java -version

                    echo "Maven Version"
                    mvn -version

                    echo "Docker Version"
                    docker --version
                '''
            }
        }

        stage('Build WAR') {
            steps {
                sh '''
                    mvn clean package
                    ls -lh target/
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                    -t ${IMAGE_NAME}:latest \
                    .
                '''
            }
        }

        stage('Stop Existing Container') {
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

        stage('Verify Deployment') {
            steps {
                sh '''
                    sleep 10
                    docker ps
                    docker logs ${CONTAINER_NAME} | tail -20
                '''
            }
        }
    }

    post {

        success {
            echo '====================================='
            echo 'Deployment Successful'
            echo "Application URL:"
            echo "http://YOUR_PUBLIC_IP:${HOST_PORT}"
            echo '====================================='
        }

        failure {
            echo '====================================='
            echo 'Deployment Failed'
            echo 'Check Console Output'
            echo '====================================='
        }

        always {
            sh '''
                echo "Current Running Containers:"
                docker ps || true
            '''
        }
    }
}
