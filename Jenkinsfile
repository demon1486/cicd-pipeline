pipeline {
    agent any // This specifies that the pipeline can run on any available agent

    stages {
        stage('Checkout') {
            steps {
                // This step checks out your source code from your Git repository
                script {
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                // Use Node.js tool defined in Jenkins Global Tool Configuration
                tool name: 'NodeJS-7.8.0', type: 'ToolInstallation'

                // This step installs project dependencies
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                // This step runs tests for your application
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                // This step builds Docker images for both "main" and "dev" branches
                sh 'docker build -t nodemain:v1.0 .'
                sh 'docker build -t nodedev:v1.0 .'
            }
        }

        stage('Container Management') {
            steps {
                // This step stops and removes any existing containers
                sh 'docker stop $(docker ps -a -q)'
                sh 'docker rm $(docker ps -a -q)'
            }
        }

        stage('Run Application in Docker') {
            steps {
                script {
                    // Set the port number based on the branch
                    def port = (env.BRANCH_NAME == 'main') ? 3000 : 3001
                    // This step runs the application in a Docker container
                    sh "docker run -d --expose $port -p $port:3000 " +
                       ((env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0')
                }
            }
        }
    }
}
