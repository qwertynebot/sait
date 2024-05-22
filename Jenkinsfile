pipeline {
    agent any

    environment {
        // Docker Hub credentials
        DOCKER_CREDENTIALS_ID = 'daca55e7-a6fa-4d2b-9308-da0105c13b36'
        DOCKER_HUB_REPO = 'darkne24'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // Database stages
        stage('Build Database Image') {
            steps {
                script {
                    def dbImage = "${DOCKER_HUB_REPO}/database:latest"
                    docker.build(dbImage, 'path/to/database')
                }
            }
        }

        stage('Push Database Image') {
            steps {
                script {
                    def dbImage = "${DOCKER_HUB_REPO}/database:latest"
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image(dbImage).push()
                    }
                }
            }
        }

        // Backend stages
        stage('Build Backend Image') {
            steps {
                script {
                    def backendImage = "${DOCKER_HUB_REPO}/backend:latest"
                    docker.build(backendImage, 'path/to/backend')
                }
            }
        }

        stage('Push Backend Image') {
            steps {
                script {
                    def backendImage = "${DOCKER_HUB_REPO}/backend:latest"
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image(backendImage).push()
                    }
                }
            }
        }

        // Frontend stages
        stage('Build Frontend Image') {
            steps {
                script {
                    def frontendImage = "${DOCKER_HUB_REPO}/frontend:latest"
                    docker.build(frontendImage, 'path/to/frontend')
                }
            }
        }

        stage('Push Frontend Image') {
            steps {
                script {
                    def frontendImage = "${DOCKER_HUB_REPO}/frontend:latest"
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image(frontendImage).push()
                    }
                }
            }
        }

        // Deployment stages
        stage('Deploy Database Container') {
            steps {
                script {
                    sh 'docker stop database || true && docker rm database || true'
                    sh "docker run -d --name database ${DOCKER_HUB_REPO}/database:latest"
                }
            }
        }

        stage('Deploy Backend Container') {
            steps {
                script {
                    sh 'docker stop backend || true && docker rm backend || true'
                    sh "docker run -d --name backend --link database:database ${DOCKER_HUB_REPO}/backend:latest"
                }
            }
        }

        stage('Deploy Frontend Container') {
            steps {
                script {
                    sh 'docker stop frontend || true && docker rm frontend || true'
                    sh "docker run -d --name frontend --link backend:backend ${DOCKER_HUB_REPO}/frontend:latest"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build, push, and deploy succeeded!'
        }
        failure {
            echo 'Build, push, or deploy failed!'
        }
    }
}

