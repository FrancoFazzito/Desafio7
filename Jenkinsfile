pipeline {
    agent {
        label 'ansible-controller'
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
                        env.INVENTORY = 'inventories/dev/hosts.ini'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = 'inventories/staging/hosts.ini'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = 'inventories/main/hosts.ini'
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
        stage('Copy Files') {
            steps {
                script {
                    // Crear directorios en el agente remoto
                    sh 'mkdir -p ~/ansible/inventories/dev'
                    sh 'mkdir -p ~/ansible/inventories/staging'
                    sh 'mkdir -p ~/ansible/inventories/main'
                    sh 'mkdir -p ~/ansible/playbook'
                    // Copiar los archivos necesarios al agente remoto
                    sh 'cp ${WORKSPACE}/inventories/dev/hosts.ini ~/ansible/inventories/dev/'
                    sh 'cp ${WORKSPACE}/inventories/staging/hosts.ini ~/ansible/inventories/staging/'
                    sh 'cp ${WORKSPACE}/inventories/main/hosts.ini ~/ansible/inventories/main/'
                    sh 'cp ${WORKSPACE}/playbook/playbook.yml ~/ansible/playbook/'
                }
            }
        }
        stage('Run Playbook') {
            steps {
                sh """
                ansible-playbook -i ~/ansible/${env.INVENTORY} ~/ansible/playbook/playbook.yml
                """
            }
        }
    }
}
