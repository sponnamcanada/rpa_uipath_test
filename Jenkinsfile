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
        UIPATH_PROJECT_PATH = '${WORKSPACE}/'
        BRANCH_NAME = "main"
        UIPATH_PACKAGE_OUTPUT_PATH = "${WORKSPACE}/Output"
        UIPATH_VERSION = "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"
    }

    // Stages for the pipeline
    stages {
        // Printing Basic Information
        stage('Preparing') {
            steps {
                echo "Jenkins Home: ${env.JENKINS_HOME}"
                echo "Jenkins URL: ${env.JENKINS_URL}"
                echo "Jenkins Job Number: ${env.BUILD_NUMBER}"
                echo "Jenkins Job Name: ${env.JOB_NAME}"
                echo "GitHub Branch Name: ${env.BRANCH_NAME}"
                echo "Using UiPath Project Path: ${env.UIPATH_PROJECT_PATH}"

                checkout scm
            }
        }

        // Build Stage
        stage('Build') {
            steps {
                echo "Building with workspace: ${env.WORKSPACE}"
                
                UiPathPack(
                    projectJsonPath: "${UIPATH_PROJECT_PATH}/project.json", // The path to project.json in your workspace
                    outputPath: "${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}", // Path where the .nupkg will be saved
                    version: [$class: 'ManualVersionEntry', version: "${UIPATH_VERSION}"], // The version to assign to the .nupkg
                    traceLevel: 'None', // Trace level (can be 'None', 'Info', 'Verbose')
                    useOrchestrator: false
                )
            }
        }

        // Deploy to Orchestrator Stage
        stage('Deploy to Orchestrator') {
            steps {
                echo "Deploying the package to Orchestrator..."

                withCredentials([string(credentialsId: 'APIUserKey', variable: 'UIPATH_API_TOKEN')]) {
                    UiPathDeploy(
                        packagePath: "${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}",  // Correct path to the .nupkg file
                        orchestratorAddress: "${env.UIPATH_ORCH_URL}", // Orchestrator URL
                        orchestratorTenant: "${env.UIPATH_ORCH_TENANT_NAME}", // Orchestrator Tenant Name
                        folderName: "${env.UIPATH_ORCH_FOLDER_NAME}", // Folder in Orchestrator
                        environments: '', // Empty environments field if not needed
                        createProcess: true, // Create a process in Orchestrator
                        apiKey: "${env.UIPATH_API_TOKEN}",  // Credential API token injected here
                        traceLevel: 'Verbose', // Trace level (can be 'None', 'Info', or 'Verbose')
                        entryPointPaths: 'Main.xaml', // Entry point for your process
	                
                    )
                }
            }
        }
    }
}
