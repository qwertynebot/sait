pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('daca55e7-a6fa-4d2b-9308-da0105c13b36')
        REPO_NAME = 'darkne24'
    }

    stages {
        stage('Build SQL Server Docker Image') {
            steps {
                script {
                    sh 'docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Qwerty-1" -p 1433:1433 --name sql111 --hostname sql1 -d mcr.microsoft.com/mssql/server:2022-latest'
                }
            }
        }

        stage('Build and Publish .NET Core Application') {
            agent {
                docker {
                    image 'mcr.microsoft.com/dotnet/sdk:6.0'
                }
            }
            steps {
                script {
                    // Restore dependencies and update the database
                    sh 'dotnet restore "ShopApi/ShopApi.csproj"'
                    sh 'dotnet tool install -g dotnet-ef --version 6.0'
                    sh 'export PATH=$PATH:/root/.dotnet/tools'
                    sh 'dotnet-ef database update --startup-project "ShopApi" --project "DAL/DAL.csproj"'

                    // Build and publish the application
                    sh 'dotnet build "ShopApi/ShopApi.csproj" -c Release -o /app/build'
                    sh 'dotnet publish "ShopApi/ShopApi.csproj" -c Release -o /app/publish'
                }
            }
        }

        stage('Build Backend Docker Image') {
            agent any
            steps {
                script {
                    // Build backend Docker image
                    dir('backend') {
                        sh 'docker build -t ${REPO_NAME}_backend .'
                    }
                }
            }
        }

        stage('Start Backend Container') {
            agent any
            steps {
                script {
                    // Start backend container
                    sh 'docker run -d --name backend -p 5034:5034 ${REPO_NAME}_backend'
                }
            }
        }

        stage('Push Backend Docker Image') {
            agent any
            steps {
                script {
                    // Push backend Docker image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub_credentials') {
                        sh 'docker push ${REPO_NAME}_backend'
                    }
                }
            }
        }

        stage('Build Frontend Docker Image') {
            agent any
            steps {
                script {
                    // Build frontend Docker image
                    dir('frontend') {
                        sh 'docker build -t ${REPO_NAME}_frontend .'
                    }
                }
            }
        }

        stage('Start Frontend Container') {
            agent any
            steps {
                script {
                    // Start frontend container
                    sh 'docker run -d --name frontend -p 80:80 ${REPO_NAME}_frontend'
                }
            }
        }

        stage('Push Frontend Docker Image') {
            agent any
            steps {
                script {
                    // Push frontend Docker image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub_credentials') {
                        sh 'docker push ${REPO_NAME}_frontend'
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker containers and images after the build
            script {
                sh 'docker stop frontend backend sql111'
                sh 'docker rm frontend backend sql111'
                sh 'docker rmi ${REPO_NAME}_frontend ${REPO_NAME}_backend'
            }
        }
    }
}

 
