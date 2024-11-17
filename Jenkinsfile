// Dhruvi Pruthvisinh Rathod - G01465151
// Jashanveer Singh - G01477180
// Namita Chougule – G01473740

pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'docker_cred'  // Docker Hub credentials ID in Jenkins
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'  // Kubeconfig secret file ID in Jenkins
        BACKEND_DOCKER_IMAGE = 'jashanveer/backend-linux'
        FRONTEND_DOCKER_IMAGE = 'jashanveer/frontend-121-linux'
        DOCKER_TAG = 'latest'
        BACKEND_DEPLOYMENT_YAML = 'backend-deployment.yaml'
        BACKEND_SERVICE_YAML = 'backend-service.yaml'
        FRONTEND_DEPLOYMENT_YAML = 'frontend-deployment.yaml'
        FRONTEND_SERVICE_YAML = 'frontend-service.yaml'
        DOCKER_USERNAME = 'jashanveer'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Authenticate with Docker Hub') {
    steps {
        withCredentials([string(credentialsId: "${DOCKER_CREDENTIALS_ID}", variable: 'DOCKER_PASSWORD')]) {
            sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
        }
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
            steps {
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                    script {
                        // Ensure KUBECONFIG environment variable is set
                        sh '''
                        export KUBECONFIG=${KUBECONFIG}
                        kubectl apply -f ${BACKEND_DEPLOYMENT_YAML}
                        kubectl apply -f ${BACKEND_SERVICE_YAML}
                        kubectl apply -f ${FRONTEND_DEPLOYMENT_YAML}
                        kubectl apply -f ${FRONTEND_SERVICE_YAML}
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Please check the logs.'
        }
    }
}
