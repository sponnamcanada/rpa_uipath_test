pipeline {
    agent any

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
        UIPATH_ORCH_API_TOKEN = "your_api_token_here" // Ensure this is set with your actual API Token
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
                echo "Using UiPath Project Path: ${env.UIPATH_PROJECT_PATH}"

                checkout scm
            }
        }

        // Build Stage
        stage('Build') {
            steps {
                echo "Building with workspace: ${env.WORKSPACE}"

                UiPathPack(
                    projectJsonPath: "${UIPATH_PROJECT_PATH}/project.json",
                    outputPath: "${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}",
                    version: [$class: 'ManualVersionEntry', version: "${UIPATH_VERSION}"],
                    traceLevel: 'Verbose',
                    useOrchestrator: false
                )
            }
        }

        // Print the details of the built package
        stage('Print Package Details') {
            steps {
                script {
                    // Print package details before deployment
                    echo "Package Details:"
                    echo "Package Path: ${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}/package.nupkg"
                    echo "Package Version: ${UIPATH_VERSION}"
                }
            }
        }

        // Deploy to Orchestrator Stage
        stage('Deploy to Orchestrator') {
            steps {
                echo "Deploying the package to Orchestrator..."

                script {
                    // Run curl to publish the package and capture the response before deployment
                    def response = sh(script: """
                        curl -X POST "${UIPATH_ORCH_URL}/odata/Packages/Publish" \\
                        -H "Authorization: Bearer ${UIPATH_ORCH_API_TOKEN}" \\
                        -F "file=@${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}/package.nupkg"
                    """, returnStdout: true).trim()

                    // Print the response after the publish request (before actual deployment step)
                    echo "Response from API (Before Deploying): ${response}"

                    // Now call UiPathDeploy plugin to deploy the package to Orchestrator
                    UiPathDeploy(
                        packagePath: "${UIPATH_PACKAGE_OUTPUT_PATH}/${env.BUILD_NUMBER}/package.nupkg", 
                        orchestratorAddress: "${UIPATH_ORCH_URL}", 
                        orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}", 
                        folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                        environments: '', 
                        createProcess: true,
                        credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'),
                        traceLevel: 'Verbose', 
                        entryPointPaths: 'Main.xaml' 
                    )
                }
            }
        }
    }
}
