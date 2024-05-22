 pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY_CREDENTIALS = credentials('daca55e7-a6fa-4d2b-9308-da0105c13b36')
        DOCKER_IMAGE_TAG = 'latest'
        MYSQL_IMAGE_NAME = 'darkne24/database'
        BACKEND_IMAGE_NAME = 'darkne24/backend'
        FRONTEND_IMAGE_NAME = 'darkne24/frontend'
    }
    
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Build and Start MySQL') {
            steps {
                script {
                    docker.build(MYSQL_IMAGE_NAME, '-f darkne24/database/Dockerfile .')
                    docker.image(MYSQL_IMAGE_NAME).withRun('-d --name mysql-container') { c ->
                        // Container started
                    }
                }
            }
        }
        
        stage('Check MySQL Health') {
            steps {
                // Script to check MySQL health
            }
        }
        
        stage('Build and Start Backend') {
            steps {
                script {
                    docker.build(BACKEND_IMAGE_NAME, '-f darkne24/backend/Dockerfile .')
                    docker.image(BACKEND_IMAGE_NAME).withRun('-d --name backend-container') { c ->
                        // Container started
                    }
                }
            }
        }
        
        stage('Check Backend Health') {
            steps {
                // Script to check Backend health
            }
        }
        
        stage('Build and Start Frontend') {
            steps {
                script {
                    docker.build(FRONTEND_IMAGE_NAME, '-f darkne24/frontend/Dockerfile .')
                    docker.image(FRONTEND_IMAGE_NAME).withRun('-d --name frontend-container') { c ->
                        // Container started
                    }
                }
            }
        }
        
        stage('Check Frontend Health') {
            steps {
                // Script to check Frontend health
            }
        }
        
        stage('Push DockerHub') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS) {
                        docker.image(MYSQL_IMAGE_NAME).push(DOCKER_IMAGE_TAG)
                        docker.image(BACKEND_IMAGE_NAME).push(DOCKER_IMAGE_TAG)
                        docker.image(FRONTEND_IMAGE_NAME).push(DOCKER_IMAGE_TAG)
                    }
                }
            }
        }
    }
}
