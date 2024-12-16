# Jenkinsfile
pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('awsCred')  // replace with your AWS credential ID
        AWS_SECRET_ACCESS_KEY = credentials('awsCred')  // replace with your AWS secret key ID
        KUBECONFIG_CONTENT = credentials('kubeconfigid')  // replace with your kubeconfig ID
    }
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // Write kubeconfig to a file
                    writeFile file: "${WORKSPACE}/kubeconfig", text: "${KUBECONFIG_CONTENT}"
                }
                sh '''
                export KUBECONFIG=${WORKSPACE}/kubeconfig
                aws eks update-kubeconfig --name test2 --region  us-east-1
                kubectl get nodes
                '''
            }
        }
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                ansiColor('xterm') {
                    sh '''
                    ansible-playbook -e "kubeconfig=${WORKSPACE}/kubeconfig" ansible/deploy.yml
                    '''
                }
            }
        }
    }
}
