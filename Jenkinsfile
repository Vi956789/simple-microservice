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
                    // Check if the "green" container exists and stop/remove if found
                    def greenExists = bat(script: 'docker ps -a -q --filter "name=simple-microservice-green"', returnStdout: true).trim()

                    if (greenExists) {
                        bat "docker stop simple-microservice-green && docker rm simple-microservice-green"
                    }

                    // Check if the "blue" container exists and stop/remove if found
                    def blueExists = bat(script: 'docker ps -a -q --filter "name=simple-microservice-blue"', returnStdout: true).trim()

                    if (blueExists) {
                        bat "docker stop simple-microservice-blue && docker rm simple-microservice-blue"
                    }

                    // Run the new container in the "green" environment (or switch based on condition)
                    bat "docker run -d --name simple-microservice-green -p 4001:3000 simple-microservice:${env.BUILD_ID}"
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
