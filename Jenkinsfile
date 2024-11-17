pipeline {
    agent any

    environment {
        BACKEND_DOCKER_IMAGE = 'jashanveer/backend-linux'
        FRONTEND_DOCKER_IMAGE = 'jashanveer/frontend-121-linux'
        DOCKER_TAG = 'latest'
        BACKEND_DEPLOYMENT_YAML = 'backend-deployment.yaml'
        BACKEND_SERVICE_YAML = 'backend-service.yaml'
        FRONTEND_DEPLOYMENT_YAML = 'frontend-deployment.yaml'
        FRONTEND_SERVICE_YAML = 'frontend-service.yaml'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Kubernetes') {
            parallel {
                stage('Deploy Backend') {
                    steps {
                        script {
                            // Update backend deployment YAML to use the existing image
                            sh """
                            kubectl set image -f ${BACKEND_DEPLOYMENT_YAML} backend=${BACKEND_DOCKER_IMAGE}:${DOCKER_TAG}
                            kubectl apply -f ${BACKEND_DEPLOYMENT_YAML}
                            kubectl apply -f ${BACKEND_SERVICE_YAML}
                            """
                        }
                    }
                }
                stage('Deploy Frontend') {
                    steps {
                        script {
                            // Update frontend deployment YAML to use the existing image
                            sh """
                            kubectl set image -f ${FRONTEND_DEPLOYMENT_YAML} frontend=${FRONTEND_DOCKER_IMAGE}:${DOCKER_TAG}
                            kubectl apply -f ${FRONTEND_DEPLOYMENT_YAML}
                            kubectl apply -f ${FRONTEND_SERVICE_YAML}
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully using existing images!'
        }
        failure {
            echo 'Deployment failed. Check logs for details.'
        }
    }
}
