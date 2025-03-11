pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        BRANCH_NAME = 'main'
        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        UIPATH_ORCH_LOGICAL_NAME = "cloud_siva_ponnam"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "Jenkins uipath"
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
                    outputPath: "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: true,
                    traceLevel: 'None'
                )
            }
        }
    }
}
