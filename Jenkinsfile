pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'weather-service:latest'
        AWS_ECR_URI = '<your-account-id>.dkr.ecr.<region>.amazonaws.com/weather-service'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --configuration Release'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    sh """
                    docker build -t ${DOCKER_IMAGE} .
                    aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin ${AWS_ECR_URI}
                    docker tag ${DOCKER_IMAGE} ${AWS_ECR_URI}
                    docker push ${AWS_ECR_URI}
                    """
                }
            }
        }

        stage('Deploy to AWS') {
            steps {
                script {
                    sh """
                    aws ecs update-service --cluster my-cluster --service weather-service --force-new-deployment
                    """
                }
            }
        }
    }
}

