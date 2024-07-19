pipeline {
    agent any
    environment {
        ANSIBLE_HOST = "172.31.189.94"
        ANSIBLE_USER = "ubuntu"
    }
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
                        env.INVENTORY = 'inventories/dev/hosts'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = 'inventories/staging/hosts'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = 'inventories/main/hosts'
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
                sshagent(credentials: ['ansible-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${ANSIBLE_USER}@${ANSIBLE_HOST} << EOF
                    mkdir -p ~/ansible/inventories
                    cp -r ${WORKSPACE}/inventories/* ~/ansible/inventories/
                    ansible-playbook -i ~/ansible/${env.INVENTORY} ${WORKSPACE}/playbook.yml
                    EOF
                    """
                }
            }
        }
    }
}

