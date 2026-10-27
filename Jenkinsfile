
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
        bat '"C:\\Users\\likhi\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" --version'
        bat 'wsl -d Ubuntu -- bash -lc "ansible --version"'
    }
}


        stage('Validate Playbook') {
            steps {
                bat 'wsl -d Ubuntu -- bash -lc "cd ~/Jenkins-Ansible-Infrastructure && ansible-playbook --syntax-check playbooks/provision.yml"'
            }
        }

        stage('Trigger Ansible Deployment') {
            steps {
                bat 'schtasks /run /tn "Jenkins-Ansible-Runner"'
                sleep time: 15, unit: 'SECONDS'
            }
        }

        stage('Verify Website') {
            steps {
                bat 'powershell -NoProfile -Command "$r = Invoke-WebRequest http://localhost:8081 -UseBasicParsing; if ($r.StatusCode -ne 200 -or $r.Content -notmatch \'Infrastructure Provisioned Successfully!\') { exit 1 }; Write-Output \'Website verification passed: HTTP 200\'"'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Jenkins triggered the Ansible deployment and verified the website.'
        }
        failure {
            echo 'FAILED: Check Console Output for errors.'
        }
    }
}
