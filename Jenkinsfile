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
        BRANCH_NAME = "main"
    }

    stages {
        stage('Print Info') {
            steps {
                echo "packagePath: S:\\jenkins workspace\\workspace\\uipathjenkinswebhook\\Output\\110\\" // Corrected path separator for Windows
                echo "orchestratorAddress: ${UIPATH_ORCH_URL}"
                echo "orchestratorTenant: ${UIPATH_ORCH_TENANT_NAME}"
                echo "folderName: ${UIPATH_ORCH_FOLDER_NAME}"

                // To check if the API key is being retrieved correctly, you can print a message
                script {
                    withCredentials([string(credentialsId: 'APIUserKey', variable: 'API_KEY')]) {
                        echo 'Successfully retrieved API key credentials' // Do not print actual API key
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
