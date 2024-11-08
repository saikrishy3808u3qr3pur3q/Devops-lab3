pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-docker-jenkins'
        DOCKER_REGISTRY = 'docker.io'  // Change to your Docker registry if needed
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
                    // Hardcode the Docker username and password directly in the Jenkinsfile
                    bat '''
                    echo 123password | docker login -u saikrishnanr942 --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the container
                    docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}").inside {
                        bat 'pytest --maxfail=1 --disable-warnings -v'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to the registry
                    bat 'docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}'
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
