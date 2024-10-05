pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "thariqos/django_backend"  // Replace with your DockerHub username and image name
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
                    // Install dependencies in a temporary container (optional step if you want to verify dependencies)
                    sh 'docker run --rm -v $(pwd):/app -w /app python:3.12-slim pip install -r requirements.txt'
                }
            }
        }

        stage('Run Django Tests') {
            steps {
                script {
                    // Run your Django tests inside the Docker container
                    sh 'docker run --rm -v $(pwd):/app -w /app python:3.12-slim python manage.py test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image of your Django project
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to DockerHub and push the image
                    docker.withRegistry('', 'dockerhub-credentials') {
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    // Use SSH to access the production server and deploy
                    sshagent (['production-server-credentials']) {
                        sh """
                        ssh user@production-server 'docker pull ${DOCKER_IMAGE}:${DOCKER_TAG} && cd /path/to/docker-compose && docker-compose down && docker-compose up -d'
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up the workspace after the pipeline
            cleanWs()
        }
    }
}
