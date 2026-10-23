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

        stage('Deploy') {
            steps {
                sh '''
                    set -eu

                    export COMPOSE_PROJECT_NAME=taskflow-devops-capstone
                    export APP_VERSION="${BUILD_NUMBER}"

                    echo "Deploying TaskFlow build ${BUILD_NUMBER}"
                    docker compose -p taskflow-devops-capstone up -d --no-build --force-recreate
                '''
            }
        }

        stage('Health check') {
            steps {
                sh '''
                    set -eu

                    for i in $(seq 1 30); do
                        if curl -fsS http://127.0.0.1:3000/api/health; then
                            echo "TaskFlow health check passed."
                            exit 0
                        fi
                        sleep 2
                    done

                    echo "Health check failed."
                    exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'TaskFlow CI/CD pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the stage logs before retrying.'
        }
    }
}
