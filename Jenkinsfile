pipeline {
    agent any

    environment {
        COMPOSE_FILE = "docker-compose.dev.yml"
        ENV_FILE = "/opt/157A-Rental-Service/.env"
        PROJECT_NAME = "157a-rental-service"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Project') {
            steps {
                sh '''
                    echo "Checking project..."

                    pwd
                    ls -la

                    test -f docker-compose.dev.yml
                    test -d backend
                    test -d frontend

                    echo "Project structure verified"
                '''
            }
        }

        stage('Docker Check') {
            steps {
                sh '''
                    docker --version
                    docker compose version
                    docker info > /dev/null

                    echo "Docker is available"
                '''
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                    if [ ! -f "$ENV_FILE" ]; then
                        echo "ERROR: Environment file not found"
                        exit 1
                    fi

                    echo "Environment file exists"
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                    docker compose \
                        -p "$PROJECT_NAME" \
                        --env-file "$ENV_FILE" \
                        -f "$COMPOSE_FILE" \
                        build
                '''
            }
        }

        stage('Stop Existing Application') {
            steps {
                sh '''
                    docker compose \
                        -p "$PROJECT_NAME" \
                        --env-file "$ENV_FILE" \
                        -f "$COMPOSE_FILE" \
                        down || true
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker compose \
                        -p "$PROJECT_NAME" \
                        --env-file "$ENV_FILE" \
                        -f "$COMPOSE_FILE" \
                        up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    sleep 10

                    docker compose \
                        -p "$PROJECT_NAME" \
                        --env-file "$ENV_FILE" \
                        -f "$COMPOSE_FILE" \
                        ps
                '''
            }
        }
    }

    post {
        success {
            echo '157A-Rental-Service deployment SUCCESSFUL'
        }

        failure {
            echo '157A-Rental-Service deployment FAILED'
        }

        always {
            echo 'Jenkins pipeline completed'
        }
    }
}
