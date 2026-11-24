
pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://52.64.195.74:9000'
        NEXUS_URL = 'http://52.64.195.74:8081'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading source code from GitHub'
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

        stage('SonarQube Scan') {
            steps {
                echo 'Running SonarQube code analysis'

                withCredentials([
                    string(
                        credentialsId: 'sonarqube-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    sh '''
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                        -Dsonar.projectKey=devops-java-app \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.token=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                echo 'Uploading JAR to Nexus Repository'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'nexus-user',
                        usernameVariable: 'NEXUS_USER',
                        passwordVariable: 'NEXUS_PASSWORD'
                    )
                ]) {
                    sh '''
                        curl --fail --show-error \
                        --user "$NEXUS_USER:$NEXUS_PASSWORD" \
                        --upload-file target/devops-java-app-1.0.jar \
                        "$NEXUS_URL/repository/devops-raw/devops-java-app-1.0.jar"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline completed successfully'
        }

        failure {
            echo 'CI Pipeline failed - check console logs'
        }
    }
}

