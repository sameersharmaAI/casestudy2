pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "streamlit_app"
        APP_PORT = "8501"
    }

    tools {
        dockerTool 'docker' // Use the Docker tool configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sameersharmaAI/casestudy2'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Since dependencies are already installed, we just install from requirements.txt
                sh 'pip3 install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest tests/'
            }
        }

        stage('Build Docker Image with Ansible') {
            steps {
                sh 'ansible-playbook -i localhost, -c local playbook.yml --tags build'
            }
        }

        stage('Stop Existing Container with Ansible') {
            steps {
                sh 'ansible-playbook -i localhost, -c local playbook.yml --tags stop'
            }
        }

        stage('Deploy Application with Ansible') {
            steps {
                sh 'ansible-playbook -i localhost, -c local playbook.yml --tags deploy'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
