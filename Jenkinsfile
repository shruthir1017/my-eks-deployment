pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('awsCred')  // use the correct AWS credential ID in Jenkins
        AWS_SECRET_ACCESS_KEY = credentials('awsCred')  // use the correct AWS secret key ID in Jenkins
        KUBECONFIG_CONTENT = credentials('kubeconfigid')  // Kubeconfig if needed (optional)
    }
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // Write kubeconfig to a file securely (avoiding Groovy string interpolation issues)
                    writeFile file: "${WORKSPACE}/kubeconfig", text: KUBECONFIG_CONTENT
                }
                sh '''
                export KUBECONFIG=${WORKSPACE}/kubeconfig

                # Check if the EKS cluster is accessible using AWS CLI
                aws eks describe-cluster --name test2 --region us-east-1
                if [ $? -ne 0 ]; then
                    echo "EKS cluster not accessible, exiting."
                    exit 1
                fi

                # Update kubeconfig and verify by getting the nodes
                aws eks update-kubeconfig --name test2 --region us-east-1
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
