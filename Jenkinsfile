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
                    // Check if the "green" container exists
                    def greenExists = bat(script: 'docker ps -a -q --filter "name=simple-microservice-green"', returnStdout: true).trim() != ''
                    def targetEnv = greenExists ? "blue" : "green"

                    // Stop and remove the old container (if it exists)
                    if (targetEnv == "green") {
                        bat 'for /f "tokens=*" %%i in (\'docker ps -a -q --filter "name=simple-microservice-blue"\') do docker stop %%i && docker rm %%i || echo Container not found'
                    } else {
                        bat 'for /f "tokens=*" %%i in (\'docker ps -a -q --filter "name=simple-microservice-green"\') do docker stop %%i && docker rm %%i || echo Container not found'
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
