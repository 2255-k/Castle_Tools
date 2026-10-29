pipeline {
    agent any

    environment {
        APP_DIR        = 'webcastle-tools-main'
        IMAGE_NAME     = 'credvault-app'
        IMAGE_TAG      = "\-\"
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
                dir("\") {
                    sh 'npm ci'
                    sh 'npm run lint'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir("\") {
                    sh "docker build -t \:\ -t \:latest ."
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh "docker rmi \:\ \:latest || true"
            }
        }
    }

    post {
        always {
            cleanWs(deleteDirs: true, notFailBuild: true)
        }
        success {
            echo "Pipeline succeeded for CredVault build #\."
        }
        failure {
            echo "Pipeline failed at stage: \"
        }
    }
}
