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
                        env.INVENTORY = 'inventories/dev/hosts.ini'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = 'inventories/staging/hosts.ini'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = 'inventories/main/hosts.ini'
                    } else {
                        error("Unknown branch: ${env.BRANCH_NAME}")
                    }
                }
                echo "Using inventory: ${env.INVENTORY}"
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
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
