pipeline {
    agent any
    options { timestamps(); disableConcurrentBuilds() }
    stages {
        stage('Checkout') { steps { checkout scm } }
        stage('Install dependencies') { steps { bat 'npm ci' } }
        stage('Automated tests') { steps { bat 'npm test' } }
        stage('Build Docker image') {
            steps { bat 'docker build -t taskflow-devops-capstone:%BUILD_NUMBER% -t taskflow-devops-capstone:latest .' }
        }
    }
    post {
        success { echo 'TaskFlow tests and Docker build completed successfully.' }
        failure { echo 'Pipeline failed. Review the stage logs.' }
    }
}
