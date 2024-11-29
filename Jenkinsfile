pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "streamlit_app"
        APP_PORT = "8501"
        PATH = "/root/ansible-env/bin:/usr/bin:${env.PATH}" // Include Ansible and Docker paths
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
                sh '''
                /root/ansible-env/bin/python -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source venv/bin/activate
                pytest tests/
                '''
            }
        }

        stage('Build Docker Image with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    /root/ansible-env/bin/ansible-playbook -i localhost, -c local playbook.yml --tags "build"
                    '''
                }
            }
        }

        stage('Stop Existing Container with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    /root/ansible-env/bin/ansible-playbook -i localhost, -c local playbook.yml --tags "stop"
                    '''
                }
            }
        }

        stage('Deploy Application with Ansible') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    /root/ansible-env/bin/ansible-playbook -i localhost, -c local playbook.yml --tags "deploy"
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            sh 'rm -rf venv'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
