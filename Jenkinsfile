pipeline{
    agent any
    environment {
        DOCKER_REPO = "rajashekar121/jenkins-ci-cd-repo"  
    }
    stages{
        stage('Cleaning Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout the Code'){
            steps{
                git branch: 'main', credentialsId: '56742909-449a-4fbf-8c97-6be138335a7a', url: 'https://github.com/Samalarajashekhar/demo-CI-CD.git'
            }
        }
        stage("Generating Docker Image"){
            steps{
                bat 'docker build --provenance=false -t %DOCKER_REPO%:%BUILD_NUMBER% .'
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
            
                    withCredentials([usernamePassword(
                        credentialsId: '6386c90b-d344-4250-8eb2-115a8de622b6',  
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_PASSWORD')]) {
        
                        // Login to Docker Hub
                        bat "docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_PASSWORD%"
                        }
                }
            }
        }
        stage("Pushing Docker Image"){
            steps{
                bat 'docker push %DOCKER_REPO%:%BUILD_NUMBER%'
                
            }
        }
    }
}
