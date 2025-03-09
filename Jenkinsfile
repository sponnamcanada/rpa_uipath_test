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
                sh 'make build'  // Replace with your actual build command
            }
        }

        // Run tests
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'make test'  // Replace with your actual test command
            }
        }

        // Deploy the application
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                sh 'make deploy'  // Replace with your actual deploy command
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
