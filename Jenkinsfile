//  GroupName: TODO
//  Jenkinsfile defines the pipeline in scm and here, there are 4 stages in
//  this CICD pipeline: Build, Docker build, Push to Docker Hub, Kubectl Get All Nodes, Deploying Rancher to single node

@NonCPS
def generateTag() {
    return new Date().format('yyyyMMdd-HHmmss')
}
// Pipeline Stages
pipeline {
    environment {
        registry = 'sakshamnayyar/survey-ngnix'
        registryCredential = '5f83bb2f-b8fd-4d76-a898-ec9823274729'
    }
    agent any

    stages {
        //  Build Stage
        stage('Build') {
            steps {
                script {
                    checkout scm
                    sh 'rm -rf *.war'
                    sh 'jar -cvf survey.war -C src/main/webapp/ . '
                    // sh 'echo ${BUILD TIMESTAMP}'
                    tag = generateTag()
                    sh 'echo $tag'
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        def customImage = docker.build('sakshamnayyar/survey-ngnix:' + tag)
                    }
                }
            }
        }
        // Push to DockerHub Stage
        stage('Push to Docker Hub') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    docker.withRegistry('', registryCredential) {
                        def image = docker.build('sakshamnayyar/survey-ngnix:' + tag, '.')
                        docker.withRegistry('', registryCredential) {
                            image.push()
                        }
                    }
                }
            }
        }
        stage('Kubectl Get All Nodes') {
            steps {
                script {
                    sh 'kubectl get deployments'
                }
            }
        }

        // Deploying Rancher to single node
        stage('Deploying Rancher to single node') {
            steps {
                script {
                	//sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl set image deployment/deployment -n swe645-namespace container-0=Sakshamnayyar/survey-ngnix:' + tag
                }
            }
        }
    }
}
