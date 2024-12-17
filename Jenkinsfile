pipeline {
    agent { label 'slave2' }
    environment {
        ANSIBLE_HOST = '44.220.254.104'  // This IP will be used directly for the Ansible inventory
        KUBECONFIG_PATH = '/home/ubuntu/.kube/config'  // Path to the Kubernetes config
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/shruthir1017/my-eks-deployment.git'
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sshagent(['jenkins-private-key']) {
                    sh '''
                    ansible-playbook -i ${ANSIBLE_HOST}, \
                                     -e kubeconfig_path=${KUBECONFIG_PATH} \
                                     ansible/deploy.yml
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}

