pipeline {
    agent { docker { image 'node:16' } } 

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Checkout') { // Stage for cloning the repository
            steps {
                checkout scm // Use the SCM configured in your Jenkins job
            }
        }

        stage('Run Cypress Tests') { // Stage for running tests within the Docker container
            steps {
                script {
                    sh "npx cypress run" // Assuming a Node.js project with npm tests
                    }
                }
            }
        }

    post { // Actions to perform after the entire pipeline finishes
        always { // Always send a build status notification
            echo "Pipeline finished." 
        }
    }
}

