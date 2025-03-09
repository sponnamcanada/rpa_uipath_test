pipeline {
    agent any  // Use any available Jenkins agent
    
    stages {
        // Checkout the code
        stage('Checkout') {
            steps {
                checkout scm  // Checkout code from the repository
            }
        }

        // Build the application
        stage('Build') {
            steps {
                echo 'Building the application...'
                
            }
        }

        // Run tests
        stage('Test') {
            steps {
                echo 'Running tests...'
                

            }
        }

        // Deploy the application
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                           }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
        }
    }
}
