pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-docker-jenkins'
        DOCKER_REGISTRY = '<your-docker-registry>'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the container
                    docker.image(DOCKER_IMAGE).inside {
                        sh 'pytest --maxfail=1 --disable-warnings -v'
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
