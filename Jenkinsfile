pipeline {
    agent any
    

    options {
        timestamps()
    }
    parameters {
        choice(
            name: 'inventory',
            choices: ['dev', 'sit', 'ci'],
            description: 'Choose the deployment environment. Default: dev.'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Show selected environment') {
            steps {
                script {
                    env.INVENTORY_FILE = "inventory/${params.inventory}.yml"
                }

                echo "Selected environment: ${params.inventory}"
                echo "Selected inventory: ${env.INVENTORY_FILE}"

                sh '''
                    test -f "$INVENTORY_FILE"
                    ansible-inventory -i "$INVENTORY_FILE" --graph
                '''
            }
        }
        stage('Connectivity and remote user') {
            steps {
                ansibleAdhoc(
                    inventory: "${env.INVENTORY_FILE}",
                    credentialsId: 'ansible-webserver-ssh',
                    hosts: 'webservers',
                    module: 'ansible.builtin.command',
                    moduleArguments: 'whoami'
                )
            }
        }
        stage('Inspect workspace') {
            steps {
                sh '''
                    pwd
                    find . -maxdepth 3 -type f | sort
                '''
            }
        }
        stage('Syntax check') {
            steps {
                sh '''
                    ansible-playbook \
                      --syntax-check \
                      -i "$INVENTORY_FILE" \
                      playbooks/play.yml
                '''
            }
        }

        stage('Ansible lint') {
            steps {
                sh '''
                    ansible-lint -t idempotency playbooks/play.yml
                '''
            }
        }
        stage('Deploy Apache') {
            steps {
                ansiblePlaybook(
                    playbook: 'playbooks/play.yml',
                    inventory: "${env.INVENTORY_FILE}",
                    credentialsId: 'ansible-webserver-ssh',
                    colorized: true
                )
            }
        }

#        stage('Deploy Apache') {
#	    steps {
#                sshagent(credentials: ['ansible-webserver-ssh']) {
#                    sh '''
#                        ansible-playbook \
#                          -i inventory/ci.yml \
#                          playbooks/play.yml
#                    '''
#                }
#            }
#        }
        stage('Verify idempotency') {
            steps {
                ansiblePlaybook(
                    playbook: 'playbooks/play.yml',
                    inventory: "${env.INVENTORY_FILE}",
                    credentialsId: 'ansible-webserver-ssh',
                    colorized: true
                )
            }
        }

#        stage('Verify idempotency') {
#	    steps {
#                sshagent(credentials: ['ansible-webserver-ssh']) {
#                    sh '''
#                        ansible-playbook \
#                          -i inventory/ci.yml \
#                          playbooks/play.yml
#                    '''
#                }
#            }
#            
#        }

    }
}
