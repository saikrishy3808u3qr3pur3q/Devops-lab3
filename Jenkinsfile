pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-docker-jenkins'
        DOCKER_REGISTRY = 'docker.io'  // Use Docker Hub or a different registry if needed
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Login to Docker Hub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
            script {
                // Docker login with password securely passed via stdin
                bat """
                echo | set /p dummy=$DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                """
            }
        }
    }
}

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with the correct tag (latest)
                    bat "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the container
                    bat "docker run ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest pytest --maxfail=1 --disable-warnings -v"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to the registry
                    bat "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after the job finishes
        }
    }
}
