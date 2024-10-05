pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "thariqos/django_backend"  // Replace with your DockerHub image name
        DOCKER_TAG = "latest"
        REGISTRY_CREDENTIALS = credentials('dockerhub-credentials')  // Store DockerHub credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the GitHub repository
                git branch: 'main', url: 'https://github.com/osthariq/Django_Backend.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies using pip in Windows
                    bat 'docker run --rm -v %cd%:/app -w /app python:3.12-slim pip install -r requirements.txt'
                }
            }
        }

        stage('Run Django Tests') {
            steps {
                script {
                    // Run Django tests on Windows using Docker
                    bat 'docker run --rm -v %cd%:/app -w /app python:3.12-slim python manage.py test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image using Windows-compatible commands
                    bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to DockerHub
                    docker.withRegistry('', 'dockerhub-credentials') {
                        bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    // Use SSH (or a Windows equivalent) to deploy on production
                    // This assumes that SSH agent is configured and working on the Windows machine
                    sshagent (['production-server-credentials']) {
                        bat """
                        ssh user@production-server 'docker pull ${DOCKER_IMAGE}:${DOCKER_TAG} && cd /path/to/docker-compose && docker-compose down && docker-compose up -d'
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}
