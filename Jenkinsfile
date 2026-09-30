pipeline {
    agent any

    environment {
        IMAGE_NAME = "leo-app"
        CONTAINER_NAME = "leo-running"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Stage 1: Pulling latest code from GitHub...'
                checkout scm
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Stage 2: Running tests — if this fails, pipeline stops here...'
                sh 'docker run --rm -v "$PWD":/app -w /app python:3.11-slim sh -c "pip install --no-cache-dir -r requirements.txt && pytest test_app.py -v"'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Stage 3: Building Docker image...'
                sh "docker build -t ${IMAGE_NAME}:latest ."
                sh "docker images ${IMAGE_NAME}"
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Stage 4: Stopping old container and deploying new one...'
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
                sh "docker run -d --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest"
                echo 'Deployment complete!'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline succeeded! App is deployed and running.'
        }

        failure {
            echo '❌ Pipeline FAILED. Check the stage logs above for details.'
        }

        always {
            echo 'Pipeline finished. Check Jenkins dashboard for full history.'
        }
    }
}
