pipeline {
    agent any

    environment {
        // UiPath OAuth credentials
        CLIENT_ID = '608100fa-5eb9-4966-bb84-e0922ace7ad0'       // Replace with your client ID from UiPath
        CLIENT_SECRET = '$at7#iQI#UJ5WGxLt' // Replace with your client secret from UiPath
        MAJOR = '1'
        MINOR = '0'
        UIPATH_ORCH_URL = "https://cloud.uipath.com"
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
                        --data-urlencode "grant_type=client_credentials" ^
                        --data-urlencode "client_id=${CLIENT_ID}" ^
                        --data-urlencode "client_secret=${CLIENT_SECRET}" ^
                        --data-urlencode "scope=OR.Administration OR.Administration.Read OR.Administration.Write OR.Assets OR.Assets.Read OR.Assets.Write OR.Execution OR.Execution.Read OR.Execution.Write OR.Folders OR.Folders.Read OR.Folders.Write OR.Jobs OR.Jobs.Read OR.Jobs.Write OR.License OR.License.Read OR.License.Write OR.Machines OR.Machines.Read OR.Machines.Write OR.Monitoring OR.Queues OR.Queues.Read OR.Queues.Write OR.Robots OR.Robots.Read OR.Robots.Write OR.Settings OR.Settings.Read OR.Settings.Write OR.Tasks OR.Tasks.Read OR.Tasks.Write OR.Users OR.Users.Read OR.Users.Write" ^
                        --data-urlencode "audience=${UIPATH_ORCH_URL}" ^
                        -v
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
