pipeline {

    agent any

    environment {
        VM_IP = "20.98.72.32"
        SSH_CREDENTIAL = "azure-vm"
        REMOTE_USER = "azureuser"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                echo "===== BUILD ====="
                mvn clean compile
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "===== TEST ====="
                mvn test
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                echo "===== PACKAGE ====="
                mvn package
                '''
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'Amazon-Web/target/*.war', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {

                sshagent(credentials: ['azure-vm']) {

                    sh '''

                    echo "===== COPYING WAR ====="

                    scp -o StrictHostKeyChecking=no \
                    Amazon-Web/target/*.war \
                    ${REMOTE_USER}@${VM_IP}:/tmp/Amazon.war

                    echo "===== DEPLOYING ====="

                    ssh -o StrictHostKeyChecking=no \
                    ${REMOTE_USER}@${VM_IP} << EOF

                    sudo cp /tmp/Amazon.war /var/lib/tomcat10/webapps/Amazon.war

                    sudo systemctl restart tomcat10

                    EOF

                    '''

                }

            }
        }

        stage('Health Check') {

            steps {

                sh '''

                echo "Waiting for Tomcat..."

                sleep 20

                curl -I http://${VM_IP}:8080/Amazon/

                '''

            }

        }

    }

    post {

        success {

            echo "Pipeline Successful"

        }

        failure {

            echo "Pipeline Failed"

        }

    }

}
