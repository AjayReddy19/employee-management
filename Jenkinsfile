pipeline {
    agent any

    stages {

        stage('Build Application') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

    }
}

