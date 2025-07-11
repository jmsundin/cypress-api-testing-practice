pipeline {
    agent any // Specifies that the pipeline can run on any available Jenkins agent

    environment { // Define environment variables specific to this pipeline
        // Define your desired local image name
        DOCKER_IMAGE_NAME = "jenkins-cypress-container"
	BUILD_NUMBER = "0.0.1"
    }

    stages {
        stage('Checkout') { // Stage for cloning the repository
            steps {
                checkout scm // Use the SCM configured in your Jenkins job
            }
        }

        stage('Build Docker Image Locally') { // Stage for building the Docker image
            steps {
                script {
                    // Build the image and tag it for local use
                    dockerImage = docker.build "${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}" 
                }
            }
        }

        stage('Run Tests') { // Stage for running tests within the Docker container
            steps {
                script {
                    dockerImage.inside {
                        sh "npx cypress run" // Assuming a Node.js project with npm tests
                    }
                }
            }
        }

        stage('Run Locally (Optional)') { // Optional stage to run the built image locally
            when {
                expression { currentBuild.currentResult == 'SUCCESS' } // Only run if tests passed
            }
            steps {
                script {
                    sh "docker run -d --name jenkins-cypress-container -p 8081:80 ${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}" 
                }
            }
        }

        stage('Cleanup') { // Optional stage for cleaning up unused Docker images and containers
            steps {
                sh "docker stop jenkins-cypress-container" // Stop the running container
                sh "docker rm jenkins-cypress-container" // Remove the container
                sh "docker rmi ${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}" // Remove the image
            }
        }
    }

    post { // Actions to perform after the entire pipeline finishes
        always { // Always send a build status notification
            echo "Pipeline finished. Status: ${currentBuild.currentResult}" 
        }
    }
}

