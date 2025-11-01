pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "my-local-app"
        BUILD_COUNT_THRESHOLD = 5
    }

    stages {
        
        stage('Code') {
            steps {
                script {
                    echo "Checking build number..."
                    if (env.BUILD_NUMBER.toInteger() < BUILD_COUNT_THRESHOLD.toInteger()) {
                        error "Build number (${env.BUILD_NUMBER}) is less than ${BUILD_COUNT_THRESHOLD}. Failing the build."
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building Java application...'
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying Docker container..."
                    
                    // Stop and remove previous container if exists
                    sh "docker stop ${IMAGE_NAME} || true"
                    sh "docker rm ${IMAGE_NAME} || true"
                    
                    // Run fresh container
                    sh "docker run -d --name ${IMAGE_NAME} -p 8080:8080 ${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
    }
    
    post {
        success {
            echo "Deployment was successful!"
        }
        failure {
            echo "Build or deployment failed. Please check logs."
        }
    }
}
