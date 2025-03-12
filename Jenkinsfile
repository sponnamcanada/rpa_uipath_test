pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/cloud_siva_ponnam/DefaultTenant/orchestrator_"
        UIPATH_ORCH_LOGICAL_NAME = "cloud_siva_ponnam"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "jenkins uipath"
        BRANCH_NAME = "main"
    }

    stages {
        // Printing Basic Information
        stage('Preparing') {
            steps {
                echo "Jenkins Home: ${env.JENKINS_HOME}"
                echo "Jenkins URL: ${env.JENKINS_URL}"
                echo "Jenkins Job Number: ${env.BUILD_NUMBER}"
                echo "Jenkins Job Name: ${env.JOB_NAME}"
                echo "GitHub Branch Name: ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        // Build Stage
        stage('Build') {
            steps {
                echo "Building with workspace: ${WORKSPACE}"
                UiPathPack(
                    outputPath: "Output/${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: true,
					credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: “APIUserKey”],
                    traceLevel: 'Verbose'
					
                )
            }
        }

        // Test Stage
        stage('Test') {
            steps {
                echo 'Testing the workflow...'
            }
        }

        // Deploy to Production Stage
        stage('Deploy to Production') {
            steps {
                echo 'Deploy to Production'
                // Add deployment steps here (if any)
				echo "Project path:${WORKSPACE}/Output/${env.BUILD_NUMBER}"
				echo "APIUserKey"

				
            }
        }
    }

    // Options
    options {
        // Timeout for pipeline
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        //always {
            // Clean workspace if successful
          //  cleanWs()
        //}
    }
}
