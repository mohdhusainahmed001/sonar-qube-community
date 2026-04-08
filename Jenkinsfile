pipeline {
    agent any

    tools {
        // This must match the 'Name' you gave Maven in Manage Jenkins > Tools
        maven 'Maven3'
    }

    environment {
        // Defines the SonarQube server name configured in Manage Jenkins > System
        SONAR_SERVER_NAME = 'SonarQube'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mohdhusainahmed001/onextel-devops.git'
            }
        }

        stage('Build & Test') {
            steps {
                // Generates the classes SonarQube needs for analysis
                sh 'mvn clean compile test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // This block injects the server URL and Secret Token automatically
                withSonarQubeEnv("${SONAR_SERVER_NAME}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                // Optional: Pipeline waits for SonarQube to finish calculating metrics
                // Requires a Webhook configured in SonarQube
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
        success {
            echo 'Build and Analysis completed successfully!'
        }
        failure {
            echo 'Build failed. Check the logs and SonarQube connectivity.'
        }
    }
}
