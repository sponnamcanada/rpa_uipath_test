pipeline {
    agent any

    environment {
        // UiPath OAuth credentials
        CLIENT_ID = '608100fa-5eb9-4966-bb84-e0922ace7ad0'       // Replace with your client ID from UiPath
        CLIENT_SECRET = '$at7#iQI#UJ5WGxLt' // Replace with your client secret from UiPath
		 MAJOR = '1'
        MINOR = '0'
        UIPATH_ORCH_URL = "https://cloud.uipath.com/cloud_siva_ponnam/DefaultTenant/orchestrator_"
        UIPATH_ORCH_LOGICAL_NAME = "cloud_siva_ponnam"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "jenkins uipath"
        BRANCH_NAME = "main"
        PACKAGE_PATH = 'S:\\jenkins workspace\\workspace\\uipathjenkinswebhook\\Output\\' 
    }

    stages {
        stage('Authenticate with UiPath Orchestrator') {
            steps {
                script {
                    // Use curl to get OAuth token from UiPath Orchestrator
                    def authResponse = sh(script: """
                        curl -X POST "https://cloud.uipath.com/identity_/connect/token" ^
                        -H "Content-Type: application/x-www-form-urlencoded" ^
                        -d "grant_type=client_credentials&client_id=${CLIENT_ID}&client_secret=${CLIENT_SECRET}&scope=robot&audience=${ORCHESTRATOR_URL}"
                    """, returnStdout: true).trim()

                    // Parse the response and extract the access token
                    def jsonResponse = readJSON text: authResponse
                    def accessToken = jsonResponse.access_token
                    
                    // Set the token to an environment variable for later stages
                    env.ACCESS_TOKEN = accessToken
                    echo "OAuth Token obtained successfully."
                }
            }
        }

        stage('Deploy Package to UiPath Orchestrator') {
            steps {
                script {
                    // Deploy the package using UiPathDeploy, with OAuth token
                    UiPathDeploy(
                        packagePath: PACKAGE_PATH, 
                        orchestratorAddress: "${UIPATH_ORCH_URL}", 
                        orchestratorTenant: "${UIPATH_ORCH_LOGICAL_NAME}", 
                        folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                        environments: "",  // You can specify environments if needed
                        credentials: [$class: 'OAuthAuthenticationEntry', accessToken: "${env.ACCESS_TOKEN}"], // Use OAuth token here
                        entryPointPaths: 'Main.xaml', // Entry point for the process (if needed)
                        createProcess: true,  // Set to true if you want to create a process in Orchestrator
                        traceLevel: 'Verbose'
                    )
                    echo "Package deployment initiated."
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to UiPath Orchestrator succeeded!'
        }
        failure {
            echo 'Deployment to UiPath Orchestrator failed.'
        }
    }
}
