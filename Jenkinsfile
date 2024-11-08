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
                    // Check if the green container exists and is running
                    def greenExists = bat(script: 'docker ps -q --filter name=simple-microservice-green', returnStdout: true).trim() != ''
                    def targetEnv = greenExists ? "blue" : "green"

                    // Stop and remove the old container (if exists)
                    if (targetEnv == "green") {
                        bat 'docker stop simple-microservice-blue || echo Container not found'
                        bat 'docker rm simple-microservice-blue || echo Container not found'
                    } else {
                        bat 'docker stop simple-microservice-green || echo Container not found'
                        bat 'docker rm simple-microservice-green || echo Container not found'
                    }
                    
                    // Run the new container in the target environment
                    bat "docker run -d --name simple-microservice-${targetEnv} -p 4001:3000 simple-microservice:${env.BUILD_ID}"
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
