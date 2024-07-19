pipeline {
    agent any
    stages {
        stage('Print Environment Variables') {
            steps {
                script {
                    // Imprimir todas las variables de entorno
                    env.each { key, value ->
                        echo "${key} = ${value}"
                    }
                }
            }
        }
        stage('Preparation') {
            steps {
                script {
                    // Definir el entorno en función de la branch
                    if (env.BRANCH_NAME == 'dev') {
                        env.INVENTORY = '~/ansible/inventories/dev/hosts'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = '~/ansible/inventories/staging/hosts'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = '~/ansible/inventories/main/hosts'
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
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'ansible-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                        ansible-playbook -i ${env.INVENTORY} playbook.yml --private-key ${SSH_KEY}
                        """
                    }
                }
            }
        }
    }
}
