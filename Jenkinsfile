pipeline {
    agent any

    tools {
        // Must match the 'Name' in Manage Jenkins > Tools
        maven 'Maven3'
    }

    environment {
        // UPDATED: Now matches your specific server name in Jenkins System settings
        SONAR_SERVER_NAME = 'sonar-token'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mohdhusainahmed001/onextel-devops.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean compile test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Uses the 'sonar-token' installation name
                withSonarQubeEnv("${SONAR_SERVER_NAME}") {
                    // Force the token injection into the Maven command
                    sh 'mvn sonar:sonar -Dsonar.token=${SONAR_AUTH_TOKEN}'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and Analysis completed successfully!'
        }
        failure {
            echo 'Build failed. Check the name configuration or credentials.'
        }
    }
}
