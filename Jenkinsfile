pipeline {
    agent { label 'node1' }
    
    tools {
        maven 'Maven-3.8.9'
        jdk 'java'
    }

    environment {
        IMAGE_NAME = "mohamedmahmoud00/python-iti-main-jenkins"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Mohammed-Mahmoud0/java-app'
            }
        }

        stage('Build') {
        steps {
                script {
                    echo "Build Number: ${currentBuild.number}"
                    if (currentBuild.number < 5) {
                        error("build number is less than 5. Stopping pipeline!")
                    }
                }
                sh "mvn clean package -DskipTests"
            }
        }


        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

    stage('Docker Login') {
        steps {
            withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                sh """
                echo "$DOCKERHUB_PASS" | docker login -u mohamedmahmoud00 --password-stdin || exit 1
                """
            }
        }
    }


        stage('Docker Push') {
            steps {
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }

        stage('Deploy') {
            steps {
                sh """
                echo "Deploying container..."

                # Stop & remove old container if exists
                docker stop java-app || true
                docker rm java-app || true

                # Run new container on port 9000 instead of 8080
                docker run -d -p 9000:8080 --name java-app ${IMAGE_NAME}:${BUILD_NUMBER}

                echo "✅ App is running on: http://<your-server-ip>:9000"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully!"
        }
        failure {
            echo "ipeline failed!"
        }
        always {
            echo " Cleaning workspace..."
            cleanWs()
        }
    }
}
