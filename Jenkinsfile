// Dhruvi Pruthvisinh Rathod - G01465151
// Jashanveer Singh - G01477180
// Namita Chougule – G01473740

pipeline {
    agent any

    environment {
        DOCKER_USERNAME = 'jashanveer'  // Your Docker Hub username
        BACKEND_DOCKER_IMAGE = 'jashanveer/spring-boot-app'  // Backend Docker image name
        FRONTEND_DOCKER_IMAGE = 'jashanveer/vue-app'  // Frontend Docker image name
        DOCKER_TAG = 'latest'  // Use the latest tag or customize
        DOCKERFILE_PATH_BACKEND = 'backend-Dockerfile'  // Path to backend Dockerfile
        DOCKERFILE_PATH_FRONTEND = 'frontend-Dockerfile'  // Path to frontend Dockerfile
        BACKEND_DEPLOYMENT_YAML = 'backend-deployment.yaml'  // Backend deployment YAML
        BACKEND_SERVICE_YAML = 'backend-service.yaml'  // Backend service YAML
        FRONTEND_DEPLOYMENT_YAML = 'frontend-deployment.yaml'  // Frontend deployment YAML
        FRONTEND_SERVICE_YAML = 'frontend-service.yaml'  // Frontend service YAML
        DOCKER_CREDENTIALS_ID = 'docker_cred'  // Jenkins credentials ID for Docker Hub
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Backend Docker Image') {
                    steps {
                        script {
                            docker.build("${BACKEND_DOCKER_IMAGE}:${DOCKER_TAG}", "-f ${DOCKERFILE_PATH_BACKEND} .")
                        }
                    }
                }
                stage('Build Frontend Docker Image') {
                    steps {
                        script {
                            docker.build("${FRONTEND_DOCKER_IMAGE}:${DOCKER_TAG}", "-f ${DOCKERFILE_PATH_FRONTEND} .")
                        }
                    }
                }
            }
        }

        stage('Push Docker Images') {
            parallel {
                stage('Push Backend Docker Image') {
                    steps {
                        script {
                            withCredentials([string(credentialsId: DOCKER_CREDENTIALS_ID, variable: 'DOCKER_CREDENTIALS')]) {
                                // Login to Docker Hub
                                sh "echo $DOCKER_CREDENTIALS | docker login --username ${DOCKER_USERNAME} --password-stdin"
                                
                                // Push Docker image to Docker Hub
                                docker.image("${BACKEND_DOCKER_IMAGE}:${DOCKER_TAG}").push()
                            }
                        }
                    }
                }
                stage('Push Frontend Docker Image') {
                    steps {
                        script {
                            withCredentials([string(credentialsId: DOCKER_CREDENTIALS_ID, variable: 'DOCKER_CREDENTIALS')]) {
                                // Login to Docker Hub
                                sh "echo $DOCKER_CREDENTIALS | docker login --username ${DOCKER_USERNAME} --password-stdin"
                                
                                // Push Docker image to Docker Hub
                                docker.image("${FRONTEND_DOCKER_IMAGE}:${DOCKER_TAG}").push()
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            parallel {
                stage('Deploy Backend') {
                    steps {
                        script {
                            sh "kubectl apply -f ${BACKEND_DEPLOYMENT_YAML}"
                            sh "kubectl apply -f ${BACKEND_SERVICE_YAML}"
                        }
                    }
                }
                stage('Deploy Frontend') {
                    steps {
                        script {
                            sh "kubectl apply -f ${FRONTEND_DEPLOYMENT_YAML}"
                            sh "kubectl apply -f ${FRONTEND_SERVICE_YAML}"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'All deployments and services successfully deployed!'
        }
        failure {
            echo 'Deployment failed. Check the logs for details.'
        }
    }
}