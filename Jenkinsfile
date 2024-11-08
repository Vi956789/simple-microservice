pipeline {
    agent any

    environment {
        IMAGE_NAME = "simple-microservice"   // Name of your Docker image
    }

    stages {
        stage('Clone Repository') {
            steps {
                 git branch: 'main', url: 'https://github.com/Vi956789/simple-microservice.git'
// Replace with your GitHub repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %IMAGE_NAME% .'  // Build the Docker image locally
                }
            }
        }

        stage('Deploy Blue Environment') {
            steps {
                script {
                    bat 'docker tag %IMAGE_NAME% %IMAGE_NAME%:blue'  // Tag the image for the Blue environment
                    bat 'docker run -d -p 8081:3000 --name node-blue %IMAGE_NAME%:blue'  // Run in Blue environment
                }
            }
        }

        stage('Deploy Green Environment') {
            steps {
                script {
                    bat 'docker tag %IMAGE_NAME% %IMAGE_NAME%:green'  // Tag the image for the Green environment
                    bat 'docker run -d -p 8082:3000 --name node-green %IMAGE_NAME%:green'  // Run in Green environment
                }
            }
        }

        stage('Trigger Blue or Green Deployment') {
            steps {
                script {
                    // Optional: Add logic to switch between Blue and Green
                    def deployTo = input message: 'Which environment to deploy?', parameters: [
                        choice(name: 'Environment', choices: ['Blue', 'Green'], description: 'Choose environment')
                    ]
                    if (deployTo == 'Blue') {
                        bat 'docker run -d -p 8081:3000 --name node-blue %IMAGE_NAME%:blue'
                    } else {
                        bat 'docker run -d -p 8082:3000 --name node-green %IMAGE_NAME%:green'
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean up workspace after job finishes
        }
    }
}
