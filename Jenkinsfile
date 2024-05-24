pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'darkne24'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
        DB_IMAGE = "${DOCKERHUB_USERNAME}/database:latest"
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/backend:latest"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/frontend:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Start Database') {
            steps {
                script {
                    def dbImage = docker.build(DB_IMAGE, 'database')
                    dbImage.run('-d --name database')
                }
            }
        }

        stage('Build and Start Backend') {
            steps {
                script {
                    def backendImage = docker.build(BACKEND_IMAGE, 'backend')
                    backendImage.run('-d --name backend --link database:db')
                }
            }
        }

        stage('Build and Start Frontend') {
            steps {
                script {
                    def frontendImage = docker.build(FRONTEND_IMAGE, 'frontend')
                    frontendImage.run('-d --name frontend --link backend:backend')
                }
            }
        }

        stage('Push Images') {
            steps {
                script {
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                        docker.image(DB_IMAGE).push()
                        docker.image(BACKEND_IMAGE).push()
                        docker.image(FRONTEND_IMAGE).push()
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                    docker.image(DB_IMAGE).remove()
                    docker.image(BACKEND_IMAGE).remove()
                    docker.image(FRONTEND_IMAGE).remove()
                }
            }
        }
    }
}

