pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('awsCred')  // replace with your AWS access key ID credential ID
        AWS_SECRET_ACCESS_KEY = credentials('awsCred')  // replace with your AWS secret key ID credential ID
        KUBECONFIG_CONTENT = credentials('kubeconfigid')  // replace with your kubeconfig credential ID
    }
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // Write kubeconfig to a file securely (avoiding Groovy string interpolation issues)
                    writeFile file: "${WORKSPACE}/kubeconfig", text: KUBECONFIG_CONTENT
                }
                sh '''
                # Export the KUBECONFIG to point to the newly written kubeconfig file
                export KUBECONFIG=${WORKSPACE}/kubeconfig

                # Verify if the EKS cluster is accessible
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
