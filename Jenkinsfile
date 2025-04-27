pipeline {
    agent any

    environment {
        AWS_REGION = 'il-central-1'
        TARGET_GROUP_ARN = 'arn:aws:elasticloadbalancing:il-central-1:314525640319:targetgroup/tg-umat-haash/d6712b9674576b51'
        INSTANCE_PRIVATE_IP = "${params.HOST}"
    }

    parameters {
        string(name: 'HOST', defaultValue: 'dor.aws.cts.care', description: 'Private IPv4 or hostname of the EC2 instance')
        string(name: 'PORT', defaultValue: '6789', description: 'Application port')
        string(name: 'USER', defaultValue: 'ubuntu', description: 'SSH user')
    }

    stages {
        stage('Add host to known_hosts') {
            steps {
                sshagent(['ansible-ssh-key']) {
                    sh '''
                        mkdir -p ~/.ssh
                        chmod 700 ~/.ssh
                        ssh-keyscan -H ${HOST} >> ~/.ssh/known_hosts
                    '''
                }
            }
        }

        stage('Build') {
            steps {
                sshagent(['ansible-ssh-key']) {
                    script {
                        def invContent = "[server]\n${HOST} ansible_user=${USER}"
                        writeFile file: 'inventory.ini', text: invContent

                        def envContent = "PORT=${PORT}"
                        writeFile file: '.env', text: envContent

                        sh 'ansible-playbook -i inventory.ini playbook.yaml'
                    }
                }
            }
        }

        stage('Add to TG') {
            steps {
                script {
                    def instanceId = sh(
                        script: """
                            aws ec2 describe-instances \
                                --region ${AWS_REGION} \
                                --filters Name=private-ip-address,Values=${INSTANCE_PRIVATE_IP} \
                                --query 'Reservations[].Instances[].InstanceId' \
                                --output text
                        """,
                        returnStdout: true
                    ).trim()

                    if (!instanceId) {
                        error "🔍 No EC2 instance found with private IP ${INSTANCE_PRIVATE_IP}"
                    }

                    sh """
                        aws elbv2 register-targets \
                            --region ${AWS_REGION} \
                            --target-group-arn ${TARGET_GROUP_ARN} \
                            --targets Id=${instanceId}
                    """
                }
            }
        }
    }
}

