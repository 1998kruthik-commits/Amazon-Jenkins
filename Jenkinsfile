pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'Amazon-Web/target/*.war', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy stage - add SSH deployment here'
            }
        }

        stage('Health Check') {
            steps {
                echo 'Health check - add curl command here'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
