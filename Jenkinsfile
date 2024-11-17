pipeline {
    agent any

    environment {
        DOCKER_USERNAME = 'jashanveer'  // Docker Hub username
        BACKEND_DOCKER_IMAGE = 'jashanveer/backend-linux'  // Backend Docker image name
        FRONTEND_DOCKER_IMAGE = 'jashanveer/frontend-121-linux'  // Frontend Docker image name
        DOCKER_TAG = 'latest'  // Docker image tag
        BACKEND_DEPLOYMENT_YAML = 'backend-deployment.yaml'  // Backend deployment YAML
        BACKEND_SERVICE_YAML = 'backend-service.yaml'  // Backend service YAML
        FRONTEND_DEPLOYMENT_YAML = 'frontend-deployment.yaml'  // Frontend deployment YAML
        FRONTEND_SERVICE_YAML = 'frontend-service.yaml'  // Frontend service YAML
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Pull Docker Images') {
            parallel {
                stage('Pull Backend Docker Image') {
                    steps {
                        sh "docker pull ${BACKEND_DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
                stage('Pull Frontend Docker Image') {
                    steps {
                        sh "docker pull ${FRONTEND_DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            parallel {
                stage('Deploy Backend') {
                    steps {
                        sh "kubectl apply -f ${BACKEND_DEPLOYMENT_YAML}"
                        sh "kubectl apply -f ${BACKEND_SERVICE_YAML}"
                    }
                }
                stage('Deploy Frontend') {
                    steps {
                        sh "kubectl apply -f ${FRONTEND_DEPLOYMENT_YAML}"
                        sh "kubectl apply -f ${FRONTEND_SERVICE_YAML}"
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