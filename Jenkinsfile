pipeline {
    agent any
    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm ci'
            }
        }

        stage('Automated tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker build -t taskflow-devops-capstone:${BUILD_NUMBER} -t taskflow-devops-capstone:latest .'
            }
        }
    }

    post {
        success {
            echo 'TaskFlow tests and Docker build completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the stage logs.'
        }
    }
}
