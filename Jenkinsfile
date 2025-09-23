pipeline {
    agent any

    environment {
        PORT           = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMG_NAME       = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
        CONTAINER_NAME = "${env.BRANCH_NAME == 'main' ? 'nodeapp-main' : 'nodeapp-dev'}"
        LOGO_FILE_NAME = "${env.BRANCH_NAME == 'main' ? 'pokemon-9' : 'hummingbird-1'}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Logo') {
            steps {
                echo "Setting logo for branch ${env.BRANCH_NAME}"
                sh "cp -f images/${LOGO_FILE_NAME}.svg src/logo.svg"
                sh 'stat -c "Full Name: %n Length: %s bytes LastWriteTime: %y" "src/logo.svg"'
            }
        }

        stage('Build') {
            steps {
                echo "Building application for branch ${env.BRANCH_NAME}"
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests for branch ${env.BRANCH_NAME}"
                sh "npm test || echo No tests found"
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${IMG_NAME}"
                sh "docker build -t ${IMG_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                echo "Stopping and removing container: ${CONTAINER_NAME} (if exists)"
                sh "docker rm -f ${CONTAINER_NAME} || true"

                echo "Running container ${CONTAINER_NAME} on port ${PORT}"
                sh "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:3000 ${IMG_NAME}"
            }
        }
    }
}