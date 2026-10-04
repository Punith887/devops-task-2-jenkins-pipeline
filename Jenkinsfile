pipeline {
    agent any

    environment {
        IMAGE_NAME = 'simple-web-app'
        CONTAINER_NAME = 'web-app-container'
        PORT = '8081'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker Image...'
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Test') {
            steps {
                echo 'Running basic validation tests...'
                // Simple smoke test to check if docker image exists
                sh "docker image inspect ${IMAGE_NAME}:${BUILD_NUMBER} > /dev/null"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application container...'
                sh """
                # Stop and remove previous container if it exists
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                
                # Run the new container
                docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline succeeded! App running at http://localhost:${PORT}"
        }
        failure {
            echo "Pipeline failed. Check stage logs."
        }
    }
}