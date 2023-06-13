pipeline {
    agent any

    stages {
        stage('Pull Source') {
            steps {
                git 'https://github.com/94tiger/spring_test.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }
    }
}
