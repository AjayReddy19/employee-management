pipeline {
    agent any

    environment {
        IMAGE_NAME = "ajayreddy19/employee-management"
    }

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

	stage('SonarQube Analysis') {
   	     steps {
        	withSonarQubeEnv('SonarQube') {
            		sh '''
            		./mvnw clean verify sonar:sonar \
              		-Dsonar.projectKey=employee-management
            		'''
        		}
    		}
	}

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                    '''
                }
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
                sh 'docker run -d -p 8081:8080 --name employee-management ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
