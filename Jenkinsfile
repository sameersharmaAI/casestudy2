pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "streamlit_app" // Docker image name
        APP_PORT = "8501" // Port for the Streamlit app
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git branch: 'main', url: 'https://github.com/sameersharmaAI/casestudy2.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Ensure Python dependencies are installed for tests
                bat '''
                python3 -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                // Run pytest tests
                bat '''
                source venv/bin/activate
                pytest tests/
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                bat '''
                docker build -t ${DOCKER_IMAGE} .
                '''
            }
        }

        stage('Stop Existing Container') {
            steps {
                // Stop and remove the existing container if it's running
                bat '''
                docker ps -q --filter "name=${DOCKER_IMAGE}" | grep -q . && docker stop ${DOCKER_IMAGE} && docker rm ${DOCKER_IMAGE} || true
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                // Run the Streamlit app container
                bat '''
                docker run -d --name ${DOCKER_IMAGE} -p ${APP_PORT}:${APP_PORT} ${DOCKER_IMAGE}
                '''
            }
        }
    }

    post {
        always {
            // Cleanup virtual environment
            bat '''
            rm -rf venv
            '''
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
