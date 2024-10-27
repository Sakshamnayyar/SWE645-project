/*
//TODO
*/

pipeline {
    agent any

    environment {
        // Update these variables with your actual information
        DOCKER_USERNAME = 'Sakshamnayyar'
        DOCKER_IMAGE = 'Sakshamnayyar/survey-ngnix' // Replace with your Docker image name
        DOCKER_TAG = 'latest_1.3' // Using the latest tag or you can use ${env.BUILD_ID} for unique tagging
        DOCKERFILE_PATH = 'Dockerfile' // Path to Dockerfile in your GitHub repository
        DEPLOYMENT_YAML_PATH = 'deployment.yaml' // Path to your Kubernetes deployment file in the repo
        SERVICE_YAML_PATH = 'service.yaml' // Path to your Kubernetes service file in the repo
        DOCKER_CREDENTIALS_ID = '5f83bb2f-b8fd-4d76-a898-ec9823274729' // Make sure this matches the ID in Jenkins credentials

    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
    steps {
        script {
            // Get Docker Hub credentials
            withCredentials([string(credentialsId: 'docker_cred', variable: 'DOCKER_CREDENTIALS')]) {
                // Login to Docker Hub
                sh "echo \$DOCKER_CREDENTIALS | docker login --username ${DOCKER_USERNAME} --password-stdin"

                // Push Docker image to Docker Hub
                docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
            }
        }
    }
}


        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy the Kubernetes deployment and service
                    sh "kubectl apply -f ${DEPLOYMENT_YAML_PATH}"
                    sh "kubectl apply -f ${SERVICE_YAML_PATH}"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
