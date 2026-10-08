pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
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
                    ansible-playbook --syntax-check playbooks/play.yml
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
                sh '''
                    ansible-playbook playbooks/play.yml
                '''
            }
        }

        stage('Verify idempotency') {
            steps {
                sh '''
                    ansible-playbook playbooks/play.yml
                '''
            }
        }
    }
}
