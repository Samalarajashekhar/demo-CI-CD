pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                git branch: 'main', url: 'https://github.com/Samalarajashekhar/demo-CI-CD.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing npm dependencies...'
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                bat 'npm test'
            }
        }

        stage('Build') {
            steps {
                echo 'Building app...'
                bat 'npm run build'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        bat """
                            docker build -t %DOCKERHUB_USERNAME%/nodejs-app:latest .
                            echo %DOCKERHUB_PASSWORD% | docker login -u %DOCKERHUB_USERNAME% --password-stdin
                            docker push %DOCKERHUB_USERNAME%/nodejs-app:latest
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                bat 'docker run -d -p 5000:5000 --name nodejs-app yourdockerhubusername/nodejs-app:latest'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
