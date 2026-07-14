pipeline {

    agent any

    environment {
        VM_IP = "20.98.72.32"
        REMOTE_USER = "azureuser"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Debug') {
            steps {
                echo "===== DEBUG INFORMATION ====="

                sh '''
                echo "Current Directory:"
                pwd

                echo ""
                echo "Workspace Contents:"
                ls -la

                echo ""
                echo "Finding pom.xml files:"
                find . -name pom.xml

                echo ""
                echo "Java Version:"
                java -version

                echo ""
                echo "Maven Version:"
                mvn -version
                '''
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
                    echo "===== COPYING WAR TO VM ====="

                    scp -o StrictHostKeyChecking=no \
                    Amazon-Web/target/*.war \
                    ${REMOTE_USER}@${VM_IP}:/tmp/Amazon.war

                    echo "===== DEPLOYING APPLICATION ====="

                    ssh -o StrictHostKeyChecking=no \
                    ${REMOTE_USER}@${VM_IP} << EOF

                    sudo cp /tmp/Amazon.war /var/lib/tomcat10/webapps/Amazon.war

                    sudo systemctl restart tomcat10

                    exit

EOF
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                echo "===== HEALTH CHECK ====="

                sleep 20

                curl -I http://${VM_IP}:8080/Amazon/
                '''
            }
        }
    }

    post {

        success {
            echo "===================================="
            echo "Pipeline Completed Successfully"
            echo "===================================="
        }

        failure {
            echo "===================================="
            echo "Pipeline Failed"
            echo "===================================="
        }

        always {
            echo "Pipeline Execution Finished"
        }
    }
}
