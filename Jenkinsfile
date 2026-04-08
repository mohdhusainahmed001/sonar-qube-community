pipeline {
    agent any

    tools {
        // This must match the 'Name' given in Jenkins Global Tool Configuration
        maven 'Maven3'
    }

    stages {
        stage('Checkout') {
            steps {
                // Fetches code from your Git repository
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean compile test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // 'SonarQube' must match the Server Name in Jenkins System Config
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                // Optional: Pauses pipeline until SonarQube returns a result
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
