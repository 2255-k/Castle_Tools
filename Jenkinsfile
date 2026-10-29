pipeline {
    agent any

    environment {
        APP_DIR    = 'webcastle-tools-main'
        IMAGE_NAME = 'credvault-app'
        IMAGE_TAG  = "${env.BUILD_NUMBER}-${env.GIT_COMMIT.take(7)}"
    }

    tools {
        nodejs 'NodeJS-20'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 20, unit: 'MINUTES')
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install & Lint') {
            steps {
                dir("${APP_DIR}") {
                    sh 'npm ci'
                    sh 'npm run lint'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir("${APP_DIR}") {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest || true"
            }
        }
    }

    post {
        always {
            cleanWs(deleteDirs: true, notFailBuild: true)
        }
        success {
            echo "Pipeline succeeded for CredVault build #${env.BUILD_NUMBER}."
        }
        failure {
            echo "Pipeline failed at stage: ${env.STAGE_NAME}"
        }
    }
}
