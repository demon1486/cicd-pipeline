pipeline {
    agent any

    environment {
        NODEJS_VERSION = '14.17.6' // Set your desired Node.js version
        MAIN_IMAGE_TAG = 'v1.0'
        DEV_IMAGE_TAG = 'v1.0'
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Set Node.js version from the global tools configuration
                    tool name: 'NodeJS', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    sh "npm install -g n && n ${NODEJS_VERSION}"
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
                    if (currentBuild.branchName == 'main') {
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
                    sh "docker stop your_container_name || true"
                    sh "docker rm your_container_name || true"
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
