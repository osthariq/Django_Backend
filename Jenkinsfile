pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohamedmass1245/django_backend"  // Replace with your DockerHub image name
        DOCKER_TAG = "latest"
        REGISTRY_CREDENTIALS = credentials('dockerhub-credentials')  // Store DockerHub credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/osthariq/Django_Backend.git'
            }
        }

        stage('Build Docker Image with Dependencies') {
            steps {
                script {
                    // Build Docker image with dependencies installed
                    bat """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }

        stage('Run Django Tests') {
            steps {
                script {
                    // Run tests inside the newly built Docker image
                    bat """
                    docker run --rm -v %cd%:/app -w /app ${DOCKER_IMAGE}:${DOCKER_TAG} python manage.py test
                    """
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    // Log in to DockerHub and push the image
                    docker.withRegistry('', 'dockerhub-credentials') {
                        bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Check SSH Agent') {
            steps {
                script {
                    sshagent(['production-server-credentials']) {
                        bat """
                        echo SSH_AGENT_PID: %SSH_AGENT_PID%
                        echo SSH_AUTH_SOCK: %SSH_AUTH_SOCK%
                        """
            }
        }
    }
}


        stage('Deploy to Production') {
    steps {
        script {
            sshagent(['production-server-credentials']) {
                bat """
                ssh Mohamed Ismail@192.168.10.124 \"
                docker pull ${DOCKER_IMAGE}:${DOCKER_TAG} && \
                cd /path/to/docker-compose && \
                docker-compose down && \
                docker-compose up -d
                \"
                """
                        
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean up workspace
        }
    }
}
