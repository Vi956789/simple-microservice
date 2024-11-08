pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Vi956789/simple-microservice.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("simple-microservice:${env.BUILD_ID}")
                }
            }
        }
        stage('Blue-Green Deployment') {
            steps {
                script {
                    def greenExists = sh(script: 'docker ps -q --filter name=simple-microservice-green', returnStatus: true) == 0
                    def targetEnv = greenExists ? "blue" : "green"
                    sh "docker run -d --name simple-microservice-${targetEnv} -p 4000:3000 simple-microservice:${env.BUILD_ID}"
                    
                    if (targetEnv == "green") {
                        sh 'docker stop simple-microservice-blue || true && docker rm simple-microservice-blue || true'
                    } else {
                        sh 'docker stop simple-microservice-green || true && docker rm simple-microservice-green || true'
                    }
                }
            }
        }
    }
    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}