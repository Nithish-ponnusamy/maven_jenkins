pipeline {
    agent any

    tools {
        maven 'Maven 3.9.9' // Make sure Maven is configured in Jenkins
        
    }

    environment {
        GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
        GIT_SHORT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Set Version') {
            steps {
                sh 'mvn versions:set -DnewVersion=$GIT_SHORT_COMMIT'
                sh 'mvn versions:commit'
            }
        }

        stage('Build & Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Build and package successful. Commit: ${GIT_SHORT_COMMIT}"
        }
        failure {
            echo "Build failed. Please check the logs."
        }
    }
}
