pipeline {
    agent any

    parameters {
        string(name: 'HOST')
            string(name: 'PORT', defaultValue: '6789')
    }

    stages {
        stage('Add host to known_hosts') {
            steps {
                sshagent(['dor-ec2']) {
                    sh """
                        mkdir -p ~/.ssh
                        chmod 700 ~/.ssh
                        ssh-keyscan -H ${params.HOST} >> ~/.ssh/known_hosts
                    """
                }
            }
        }
                        
        stage('build') {
            steps {
                sshagent(['dor-ec2']) {
                    script {
                        def invContent = "[server]\n${params.HOST}"
                        writeFile file: 'inventory.ini', text: invContent

                        def envContent = "PORT=${params.PORT}"
                        writeFile file: '.env.', text: envContent
                        sh 'ansible-playbook -i inventory.ini playbook.yaml'
                    }
                }
            }
        }
    }
}

