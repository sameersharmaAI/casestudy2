pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "streamlit_app"
        APP_PORT = "8501"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sameersharmaAI/casestudy2'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                python -m venv venv
                venv\\Scripts\\activate.bat
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                venv\\Scripts\\activate.bat
                pytest tests/
                '''
            }
        }

        stage('Build Docker Image with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    ansible-playbook -i localhost, -c local playbook.yml --tags "build"
                    '''
                }
            }
        }

        stage('Stop Existing Container with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    ansible-playbook -i localhost, -c local playbook.yml --tags "stop"
                    '''
                }
            }
        }

        stage('Deploy Application with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    ansible-playbook -i localhost, -c local playbook.yml --tags "deploy"
                    '''
                }
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
