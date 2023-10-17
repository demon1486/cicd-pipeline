pipeline {
    agent any
    environment {
        NODEJS_VERSION = '21.0.0'
        MAIN_IMAGE_TAG = 'v1.0'
        DEV_IMAGE_TAG = 'v1.0'
    }
    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    env.PATH = "${tool 'NodeJS'}/bin:${env.PATH}"
                    env.NODE_HOME = tool 'NodeJS'
                    sh "node --version"
                    sh "npm --version"
                }
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build and Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            when {
                expression {
                    BRANCH_NAME == 'main' || BRANCH_NAME == 'dev'
                }
            }
            steps {
                script {
                    if (BRANCH_NAME == 'main') {
                        sh "docker build -t nodemain:${MAIN_IMAGE_TAG} ."
                    } else {
                        sh "docker build -t nodedev:${DEV_IMAGE_TAG} ."
                    }
                }
            }
        }
        stage('Cleanup Previous Containers') {
            when {
                expression {
                    BRANCH_NAME == 'main' || BRANCH_NAME == 'dev'
                }
            }
            steps {
                script {
                    sh "docker stop your_container_name || true"
                    sh "docker rm your_container_name || true"
                }
            }
        }
        stage('Run Docker Container') {
            when {
                expression {
                    BRANCH_NAME == 'main' || BRANCH_NAME == 'dev'
                }
            }
            steps {
                script {
                    if (BRANCH_NAME == 'main') {
                        sh "docker run -d --expose 3000 -p 3000:3000 nodemain:${MAIN_IMAGE_TAG}"
                    } else {
                        sh "docker run -d --expose 3001 -p 3001:3000 nodedev:${DEV_IMAGE_TAG}"
                    }
                }
            }
        }
    }
}
