pipeline {
    agent any

    environment {
        registry = "ais28/cicd-node-app"      // Replace with your Docker Hub username/repo
        registryCredential = 'dockerhub'      // This must match your Jenkins credential ID
        dockerImage = ''
    }

    stages {
        stage('Debug Environment') {
            steps {
                // Verify shell path and environment variables
                sh 'which sh'
                sh 'echo $PATH'
            }
        }

        stage('Cloning Git') {
            steps {
                // Clone your main branch from GitHub
                git branch: 'main', url: 'https://github.com/ais2807/cicd-node-app.git'
            }
        }

        stage('Building Docker Image') {
            steps {
                script {
                    // Build Docker image from the root directory
                    dockerImage = docker.build registry, '.'
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    // Trivy security scan for Docker image
                    sh "docker run --rm -v /Users/siatata/.docker/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image ${registry}"
                }
            }
        }

        stage('Deploying Image') {
            steps {
                script {
                    // Push Docker image to Docker Hub using Jenkins credentials
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Clean up') {
            steps {
                // Remove local Docker image to free up space
                sh "docker rmi ${registry}"
            }
        }
    }

    post {
        always {
            // Clean workspace after every build
            cleanWs()
        }
    }
}
