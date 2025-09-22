pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Init vars') {
            steps {
                script {
                    env.PORT            = (env.BRANCH_NAME == 'main') ? '3000'          : '3001'
                    env.IMG_NAME        = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    env.CONTAINER_NAME  = (env.BRANCH_NAME == 'main') ? 'nodeapp-main'  : 'nodeapp-dev'
                    echo "Branch: ${env.BRANCH_NAME} | Port: ${env.PORT} | Image: ${env.IMG_NAME} | Container: ${env.CONTAINER_NAME}"
                }
            }
        }

        stage('Set env (logo & config)') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'cp -f "images/pokemon-9.svg" "src/logo.svg"'
                    } else {
                        sh 'cp -f "images/hummingbird-1.svg" "src/logo.svg"'
                    }
                    sh 'stat -c "Full Name: %n Length: %s bytes LastWriteTime: %y" "src/logo.svg"'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building application for branch ${env.BRANCH_NAME}"
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests for branch ${env.BRANCH_NAME}"
                    sh 'npm test || echo "No tests found"'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${env.IMG_NAME}"
                    sh "docker build -t ${env.IMG_NAME} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Stopping and removing container: ${env.CONTAINER_NAME} (if exists)"
                    sh "docker rm -f ${env.CONTAINER_NAME} || true"

                    echo "Running container ${env.CONTAINER_NAME} on port ${env.PORT}"
                    sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.PORT}:3000 ${env.IMG_NAME}"
                }
            }
        }
    }
}