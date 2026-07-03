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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t employee-management:{$BUILD_NUMBER} .'
		sh 'docker push ajayreddy19/employee-management:${BUILD_NUMBER}'
            }
        }

        stage('Stop Existing Container') {
            steps {
                sh 'docker stop employee-management || true'
            }
        }

        stage('Remove Existing Container') {
            steps {
                sh 'docker rm employee-management || true'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 8081:8080 --name employee-management employee-management:v1'
            }
        }
    }
}
