pipeline {
    agent any

    tools {
        // Match the Name in Manage Jenkins > Tools
        maven 'Maven3'
    }

    environment {
        // Match the Name in Manage Jenkins > System
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
                withSonarQubeEnv("${SONAR_SERVER_NAME}") {
                    // Using the injected token for guaranteed authorization
                    sh 'mvn sonar:sonar -Dsonar.token=${SONAR_AUTH_TOKEN}'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                // Ensure SonarQube Webhook is set to: http://<jenkins-ip>:8080/sonarqube-webhook/
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
            echo 'Pipeline completed! Check your results at http://localhost:9000'
        }
        failure {
            echo 'Pipeline failed. Check SonarQube permissions for project com.onextel:my-app'
        }
    }
}
