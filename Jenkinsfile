pipeline {
    agent any

    environment {
        APP_NAME = 'nodejs-app-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build & Push Image') {
            steps {
                script {
                    // Note: Fixed usernameVariable from DOCKKUB_USERNAME to DOCKERHUB_USERNAME
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        
                        def imageNameWithBuildNumber = "${env.DOCKERHUB_USERNAME}/${env.APP_NAME}:${env.BUILD_NUMBER}"
                        def imageNameWithLatest = "${env.DOCKERHUB_USERNAME}/${env.APP_NAME}:latest"

                        echo "Building Docker image: ${imageNameWithBuildNumber}..."
                        
                        bat "docker build -t ${imageNameWithBuildNumber} ."

                        echo "Tagging image as 'latest'..."
                        bat "docker tag ${imageNameWithBuildNumber} ${imageNameWithLatest}"

                        echo "Logging in to Docker Hub..."
                        bat "echo ${env.DOCKERHUB_PASSWORD} | docker login -u ${env.DOCKERHUB_USERNAME} --password-stdin"

                        echo "Pushing image ${imageNameWithBuildNumber}..."
                        bat "docker push ${imageNameWithBuildNumber}"
                        
                        echo "Pushing image ${imageNameWithLatest}..."
                        bat "docker push ${imageNameWithLatest}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        
                        def imageNameWithLatest = "${env.DOCKERHUB_USERNAME}/${env.APP_NAME}:latest"
                        
                        echo "Deploying container ${imageNameWithLatest}..."

                        // Use '|| exit 0' to ignore errors if the container doesn't exist
                        bat "docker stop ${env.APP_NAME} || exit 0"
                        bat "docker rm ${env.APP_NAME} || exit 0"

                        echo "Starting new container..."
                        bat "docker run -d -p 5000:5000 --name ${env.APP_NAME} ${imageNameWithLatest}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Logging out of Docker Hub...'
            bat "docker logout"
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}

