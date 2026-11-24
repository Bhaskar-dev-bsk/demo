
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading source code'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Java application'
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo 'Running application tests'
                sh 'mvn test'
            }
        }

        stage('Archive') {
            steps {
                echo 'Saving build artifact'
                archiveArtifacts artifacts: 'target/*.jar',
                                 fingerprint: true
            }
        }
    }
}

