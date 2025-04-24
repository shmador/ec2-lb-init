pipeline {
    agent any

    parameters {
        string(name: 'HOST')
        string(name: 'PORT', defaultValue: '6789')
    }

    stages {
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

