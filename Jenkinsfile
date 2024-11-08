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
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        bat '''
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}").inside {
                        bat 'pytest --maxfail=1 --disable-warnings -v'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
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
