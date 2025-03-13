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
        stage('Print API Key Using Script') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'APIUserKey', variable: 'API_KEY')]) {
                        // Temporarily print API key to check (this will expose the key)
                        sh "echo ${API_KEY}"  // For Unix-based systems (Linux, MacOS)
                        // bat "echo ${API_KEY}"  // For Windows-based systems
                    }
                }
            }
        }
        
        stage('Deploy to Production') {
            steps {
                UiPathDeploy(
                    packagePath: "S:\\jenkins workspace\\workspace\\uipathjenkinswebhook\\Output\\110\\",
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
