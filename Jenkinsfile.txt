pipeline {
    agent any

    environment {
        IMAGE_NAME = "hello-api"
        CONTAINER_NAME = "hello-api-container"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dhivyar1-ux/samplecode.git'
            }
        }

        stage('Build Jar') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                docker stop %CONTAINER_NAME% || exit 0
                docker rm %CONTAINER_NAME% || exit 0
                '''
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                docker run -d -p 8081:8081 --name %CONTAINER_NAME% %IMAGE_NAME%
                '''
            }
        }
    }
}
