pipeline {
    agent any

    parameters {
        string(name: 'HOST')
        string(name: 'PORT', defaultValue: '6789')
    }

    stages {
        stage('build') {
            steps {
                sshagent(['aws']) {
                    script {
                        def content = "[server]\n${params.HOST} port=${params.PORT}"
                        writeFile file: 'inventory.ini', text: content
                        sh 'ansible-playbook -i inventory.ini playbook.yaml'
                    }
                }
            }
        }
    }
}

