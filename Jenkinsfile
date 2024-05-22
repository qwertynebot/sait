      pipeline {
    agent any
    
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Build MySQL') {
            steps {
                dir('darkne24/database') {
                    script {
                        docker.build("mysql-image", "-f Dockerfile .")
                    }
                }
            }
        }
        
        stage('Start MySQL') {
            steps {
                script {
                    sh 'docker stop $(docker ps -aq --filter name=mysql-container)'
                    sh 'docker rm $(docker ps -aq --filter name=mysql-container)'
                    sh 'docker run --name mysql-container -d mysql-image'
                }
            }
        }
        
        stage('Check MySQL Health') {
            steps {
                // Script to check MySQL health
            }
        }
        
        stage('Build Backend') {
            steps {
                dir('darkne24/backend') {
                    script {
                        docker.build("backend-image", "-f Dockerfile .")
                    }
                }
            }
        }
        
        stage('Start Backend') {
            steps {
                script {
                    sh 'docker stop $(docker ps -aq --filter name=backend-container)'
                    sh 'docker rm $(docker ps -aq --filter name=backend-container)'
                    sh 'docker run --name backend-container -d backend-image'
                }
            }
        }
        
        stage('Check Backend Health') {
            steps {
                // Script to check Backend health
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('darkne24/frontend') {
                    script {
                        docker.build("frontend-image", "-f Dockerfile .")
                    }
                }
            }
        }
        
        stage('Start Frontend') {
            steps {
                script {
                    sh 'docker stop $(docker ps -aq --filter name=frontend-container)'
                    sh 'docker rm $(docker ps -aq --filter name=frontend-container)'
                    sh 'docker run --name frontend-container -d frontend-image'
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
                    docker.withRegistry('', credentials('docker-hub-credentials')) {
                        docker.image('mysql-image').push('latest')
                        docker.image('backend-image').push('latest')
                        docker.image('frontend-image').push('latest')
                    }
                }
            }
        }
    }
}
