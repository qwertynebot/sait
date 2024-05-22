pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker-hub-credentials')
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
        
        def buildAndStartContainer(name, directory) {
            script {
                def imageName = "${directory}/Dockerfile"
                docker.build(name, "-f ${imageName} .")
                docker.image(name).withRun('-d --name ${name}-container') { c ->
                    // Container started
                }
            }
        }
        
        stage('Build and Start MySQL') {
            steps {
                buildAndStartContainer(MYSQL_IMAGE_NAME, 'darkne24/database')
            }
        }
        
        stage('Check MySQL Health') {
            steps {
                // Script to check MySQL health
            }
        }
        
        stage('Build and Start Backend') {
            steps {
                buildAndStartContainer(BACKEND_IMAGE_NAME, 'darkne24/backend')
            }
        }
        
        stage('Check Backend Health') {
            steps {
                // Script to check Backend health
            }
        }
        
        stage('Build and Start Frontend') {
            steps {
                buildAndStartContainer(FRONTEND_IMAGE_NAME, 'darkne24/frontend')
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
