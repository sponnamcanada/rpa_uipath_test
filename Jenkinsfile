pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/cloud_siva_ponnam/DefaultTenant/orchestrator_/"
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
				echo "GitHub orch Name: ${UIPATH_ORCH_TENANT_NAME}"
                checkout scm
            }
        }

        // Build Stage
        stage('Build') {
            steps {
                echo "Building with workspace: ${WORKSPACE}"
                UiPathPack(
                    outputPath: "${WORKSPACE}/Output/${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: false,
                    credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey'],
                    traceLevel: 'Verbose'
                )
            }
        }

        // Test Stage
        stage('Test') {
            steps {
                echo 'Testing the workflow...'
				echo "${WORKSPACE}/Output/${env.BUILD_NUMBER}"
            }
        }

        // Deploy to Production Stage
        stage('Deploy to Production') {
            steps {
                UiPathDeploy(
                    packagePath: "${WORKSPACE}\\Output\\${env.BUILD_NUMBER}\\",
                    orchestratorAddress:"${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                    environments: "",
                    credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey'],
					entryPointPaths:'Main.xaml',
					createProcess: true,
                    traceLevel: 'Verbose'
                )
                echo 'Deploy to Production'
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
        // Uncomment to clean workspace if successful
        // always {
        //     cleanWs()
        // }
    }
}
