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
        UIPATH_ORCH_FOLDER_NAME = "Jenkins uipath"
        UIPATH_PROJECT_PATH = "${WORKSPACE}"
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
                checkout scm
            }
        }

        // Build Stage
        stage('Build') {
            steps {
                echo "Building with workspace: ${env.WORKSPACE}"
                UiPathPack (
                    projectJsonPath: "${UIPATH_PROJECT_PATH}/project.json", // The path to project.json in your workspace
                    outputPath: "${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}", // Path where the .nupkg will be saved
                    version: [$class: 'ManualVersionEntry', version: "${UIPATH_VERSION}"],  // The version to assign to the .nupkg
                    traceLevel: 'None',  // Trace level (can be 'None', 'Info', 'Verbose')
                    useOrchestrator: false
                )
            }
        }

        // Deploy Stage
        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${env.BRANCH_NAME} to UAT"

                UiPathDeploy (
                    packagePath: "${env.WORKSPACE}\\Output\\${env.BUILD_NUMBER}",  // Correct path format for Windows
                    orchestratorAddress: "${env.UIPATH_ORCH_URL}",
                    orchestratorTenant: "${env.UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${env.UIPATH_ORCH_FOLDER_NAME}",
                    environments: '', // Keeping the environments parameter
                    credentials: [$class:'UserPassAuthenticationEntry',credentialsId: 'APIUserKey'],
                    traceLevel: 'None',
                    entryPointPaths: 'Main.xaml',
                    createProcess: true // Added the missing required parameter
                )
            }
        }
    }
}
