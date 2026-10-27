
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
        bat 'if exist C:\\Users\\likhi\\ansible-result.txt del /f /q C:\\Users\\likhi\\ansible-result.txt'
        bat 'schtasks /run /tn "Jenkins-Ansible-Runner"'

        timeout(time: 3, unit: 'MINUTES') {
            waitUntil {
                sleep time: 3, unit: 'SECONDS'
                bat(
                    returnStatus: true,
                    script: 'if not exist C:\\Users\\likhi\\ansible-result.txt exit /b 1 & findstr /x "SUCCESS" C:\\Users\\likhi\\ansible-result.txt >nul'
                ) == 0
            }
        }

        echo 'Ansible deployment reported SUCCESS.'
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
