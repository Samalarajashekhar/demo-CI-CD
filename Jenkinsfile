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
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the app...'
                sh 'npm run build'
            }
        }

        stage('Docker Build & Push') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t rajashekar121/nodejs-app:latest .
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                    docker push rajashekar121/nodejs-app:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh '''
                    docker run -d -p 5000:5000 --name nodejs-app yourdockerhubusername/nodejs-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo ' Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
