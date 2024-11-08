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
                    def greenExists = bat(script: 'docker ps -q --filter name=simple-microservice-green', returnStatus: true) == 0
                    def targetEnv = greenExists ? "blue" : "green"
                    bat "docker run -d --name simple-microservice-${targetEnv} -p 4001:3000 simple-microservice:${env.BUILD_ID}"
                    
                    if (targetEnv == "green") {
                        bat 'docker stop simple-microservice-blue || true && docker rm simple-microservice-blue || true'
                    } else {
                        bat 'docker stop simple-microservice-green || true && docker rm simple-microservice-green || true'
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
