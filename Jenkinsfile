pipeline {
    agent any
    tools {
        nodejs 'NODEJS 22' // Name of the NodeJS installation configured in Jenkins
    }
    environment {
        VPS_USER = 'root'
        VPS_IP = '104.168.5.251'
        PROJECT_DIR = '~/LorrySwap_Analytical_Dashboard'
        SSH_CREDENTIALS_ID = 'github_ssh_key' // ID for your SSH credentials
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github_ssh_key', url: 'https://github.com/DevDynasty-001/LorrySwap_Analytical_Dashboard.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('Deploy') {
            steps {
                sshagent([SSH_CREDENTIALS_ID]) {
                    sh """
                    scp -r build/ ${VPS_USER}@${VPS_IP}:${PROJECT_DIR}
                    ssh ${VPS_USER}@${VPS_IP} 'pm2 delete analytical_app || true && pm2 serve ${PROJECT_DIR}/build 4500 --name analytical_app --spa'
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}