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
                // Corrected syntax from $() to ${}
                sh """
                docker build -t ajayreddy19/employee-management:${BUILD_NUMBER} . 
                docker push ajayreddy19/employee-management:${BUILD_NUMBER}
                """
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                // Combined into one stage for cleaner logs
                sh 'docker stop employee-management || true'
                sh 'docker rm employee-management || true'
            }
        }

        stage('Run Docker Container') {
            steps {
                // Corrected to use the variable tag instead of 'v1'
                sh "docker run -d -p 8081:8080 --name employee-management ajayreddy19/employee-management:${BUILD_NUMBER}"
            }
        }
    }
}
