pipeline {
    agent { label 'ansible-controller' }
    environment {
        ANSIBLE_PRIVATE_KEY = credentials('ansible-jenkins-key')
    }
    stages {
        stage('Preparation') {
            steps {
                script {
                    // Definir el entorno en función de la branch
                    if (env.BRANCH_NAME == 'dev') {
                        env.INVENTORY = '/path/to/ansible/inventories/dev/hosts'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = '/path/to/ansible/inventories/staging/hosts'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = '/path/to/ansible/inventories/main/hosts'
                    } else {
                        error("Unknown branch: ${env.BRANCH_NAME}")
                    }
                }
                echo "Using inventory: ${env.INVENTORY}"
            }
        }
        stage('Run Playbook') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ansible-jenkins-key', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        ansible-playbook -i ${env.INVENTORY} playbook/playbook.yml --private-key=\${SSH_KEY}
                    """
                }
            }
        }
    }
}
