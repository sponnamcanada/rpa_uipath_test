pipeline {
    agent any

    environment {
        MAJOR = '1'
        MINOR = '0'
        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com"
        UIPATH_ORCH_LOGICAL_NAME = "cloud_siva_ponnam"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "jenkins uipath"
        BRANCH_NAME = "main"
    }

    stages {
        stage('Print API Key for Testing') {
            steps {
                script {
                    // This will expose the API key in the Jenkins logs (only for testing purposes)
                    withCredentials([string(credentialsId: 'APIUserKey', variable: 'API_KEY')]) {
                        echo "API Key: ${API_KEY}"
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                UiPathDeploy(
                    packagePath: "S:\\jenkins workspace\\workspace\\uipathjenkinswebhook\\Output\\110\\", // Corrected path separator for Windows
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                    environments: "",
                    credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey'],
                    entryPointPaths: 'Main.xaml',
                    createProcess: true,
                    traceLevel: 'Verbose'
                )
                echo 'Deploy to Production'
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
