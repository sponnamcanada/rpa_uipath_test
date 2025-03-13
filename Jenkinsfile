pipeline {
    agent any

    environment {
        MAJOR = '1'
        MINOR = '0'
        UIPATH_ORCH_URL = "https://cloud.uipath.com/cloud_siva_ponnam/DefaultTenant/orchestrator_"
        UIPATH_ORCH_LOGICAL_NAME = "cloud_siva_ponnam"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "jenkins uipath"
        BRANCH_NAME = "main"
    }

    stages {
        stage('Deploy to Production') {
            steps {
                withCredentials([string(credentialsId: 'APIUserKey', variable: 'API_KEY')]) {
                    // Print API Key to a file
                    bat 'echo %API_KEY% > api_key.txt'  // For Windows batch commands
                    // Print the API Key to console (temporary for testing)
                    echo "The API Key is: %API_KEY%"
                    // Optionally, print the contents of the file (for debugging)
                    bat 'type api_key.txt'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
    }
}
