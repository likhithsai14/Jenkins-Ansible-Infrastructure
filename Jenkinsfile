
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Project retrieved from GitHub'
            }
        }

        stage('Verify Tools') {
            steps {
                bat 'wsl -d Ubuntu -- bash -lc "ansible --version"'
                bat 'docker --version'
            }
        }

        stage('Validate Playbook') {
            steps {
                bat 'wsl -d Ubuntu -- bash -lc "cd ~/Jenkins-Ansible-Infrastructure && ansible-playbook --syntax-check playbooks/provision.yml"'
            }
        }

        stage('Provision Infrastructure') {
            steps {
                bat 'wsl -d Ubuntu -- bash -lc "cd ~/Jenkins-Ansible-Infrastructure && ansible-playbook playbooks/provision.yml"'
            }
        }

        stage('Verify Website') {
            steps {
                bat 'powershell -NoProfile -Command "Invoke-WebRequest http://localhost:8081 -UseBasicParsing | Select-Object -ExpandProperty StatusCode"'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Infrastructure provisioned using Jenkins and Ansible!'
        }
        failure {
            echo 'FAILED: Check Console Output for errors.'
        }
    }
}
