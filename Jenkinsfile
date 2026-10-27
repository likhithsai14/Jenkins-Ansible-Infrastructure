
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Downloading project from GitHub'
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                sh 'ansible --version'
                sh 'docker --version'
                sh 'git --version'
            }
        }

        stage('Validate Playbook') {
            steps {
                sh 'ansible-playbook --syntax-check playbooks/provision.yml'
            }
        }

        stage('Provision Infrastructure') {
            steps {
                sh 'ansible-playbook playbooks/provision.yml'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'docker ps'
                sh 'docker exec devops-demo-web nginx -t'
                sh 'curl --fail http://127.0.0.1:8081'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Infrastructure provisioned using Jenkins and Ansible.'
        }

        failure {
            echo 'FAILED: Check the Jenkins Console Output.'
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
