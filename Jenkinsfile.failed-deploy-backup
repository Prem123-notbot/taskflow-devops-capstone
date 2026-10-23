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

                    IMAGE="taskflow-devops-capstone:${BUILD_NUMBER}"
                    PREVIOUS_IMAGE=$(docker inspect taskflow-app --format '{{.Config.Image}}')

                    echo "Previous image: $PREVIOUS_IMAGE"
                    echo "Deploying image: $IMAGE"

                    docker compose up -d --no-build --force-recreate
                '''
            }
        }

        stage('Health check') {
            steps {
                sh '''
                    set +e

                    for i in $(seq 1 30); do
                        if curl -fsS http://127.0.0.1:3000/api/health >/dev/null; then
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
            echo 'Pipeline failed. Inspect the stage logs before retrying.'
        }
    }
}
