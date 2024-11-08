pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-docker-jenkins'
        DOCKER_REGISTRY = 'docker.io'  // Default Docker registry, update if you're using another registry
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials') // Name of your Docker credentials in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub using the credentials from Jenkins
                    sh 'echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image, specify the base image if needed
                    docker.build(DOCKER_IMAGE, '.')
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the container using pytest
                    docker.image(DOCKER_IMAGE).inside {
                        sh 'pytest --maxfail=1 --disable-warnings -v'
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after the build
            cleanWs()
        }
    }
}
