pipeline {
    agent any

    environment {
        NODEJS_VERSION = '21.0.0' // Set your desired Node.js version
        MAIN_IMAGE_TAG = 'v1.0'
        DEV_IMAGE_TAG = 'v1.0'
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Set Node.js version and environment variables
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
                sh 'npm install' // Command for building the Node.js application
                sh 'npm test'    // Command for testing the Node.js application
            }
        }

        stage('Build Docker Image') {
            when {
                expression { currentBuild.branchName == 'main' || currentBuild.branchName == 'dev' }
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
                expression { currentBuild.branchName == 'main' || currentBuild.branchName == 'dev' }
            }
            steps {
                script {
                    // Stop and remove existing containers
                    sh "docker stop dev_container || true"
                    sh "docker rm dev_container || true"
                }
            }
        }

        stage('Run Docker Container') {
            when {
                expression { currentBuild.branchName == 'main' || currentBuild.branchName == 'dev' }
            }
            steps {
                script {
                    if (currentBuild.branchName == 'main') {
                        sh "docker run -d --expose 3000 -p 3000:3000 nodemain:${MAIN_IMAGE_TAG}"
                    } else {
                        sh "docker run -d --expose 3001 -p 3001:3000 nodedev:${DEV_IMAGE_TAG}"
                    }
                }
            }
        }
    }
}
