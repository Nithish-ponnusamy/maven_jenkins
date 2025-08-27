pipeline {
  agent any
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
        archiveArtifacts(artifacts: 'target/*.jar', fingerprint: true)
        sh '''# Truncate the GIT_COMMIT to the first 7 characters
GIT_SHORT_COMMIT=$(echo $GIT_COMMIT | cut -c 1-7)

# Set the version using Maven
mvn versions:set -DnewVersion="$GIT_SHORT_COMMIT"
mvn versions:commit'''
        archiveArtifacts '**/target/*.jar'
      }
    }

  }
  tools {
    maven 'Maven 3.9.9'
  }
  environment {
    GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    GIT_SHORT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
  }
  post {
    success {
      echo "Build and package successful. Commit: ${GIT_SHORT_COMMIT}"
    }

    failure {
      echo 'Build failed. Please check the logs.'
    }

  }
}