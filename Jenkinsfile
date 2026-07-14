pipeline {
    agent any

    environment {
        PATH = "/usr/bin:/bin:/opt/homebrew/bin"
    }

    stages {

        stage('pull scm git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/PraveenKuber/Amazon-Jenkins.git'
            }
        }

        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
