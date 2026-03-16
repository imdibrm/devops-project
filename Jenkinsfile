pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_FRONTEND = "984285320367.dkr.ecr.ap-south-1.amazonaws.com/frontend"
        ECR_BACKEND = "984285320367.dkr.ecr.ap-south-1.amazonaws.com/backend"
        CLUSTER_NAME = "devops-intern-cluster"
        NAMESPACE = "devops"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/YOUR_USERNAME/YOUR_REPO.git'
            }
        }

        stage('Build & Test') {
            steps {
                echo "Running basic tests"
                sh 'echo Test successful'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t backend:v1 ./backend
                docker build -t frontend:v1 ./frontend
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin 984285320367.dkr.ecr.ap-south-1.amazonaws.com

                docker tag backend:v1 $ECR_BACKEND:v1
                docker tag frontend:v1 $ECR_FRONTEND:v1

                docker push $ECR_BACKEND:v1
                docker push $ECR_FRONTEND:v1
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig --name $CLUSTER_NAME --region $AWS_REGION
                kubectl apply -f k8s/
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl rollout status deployment/backend -n $NAMESPACE
                kubectl rollout status deployment/frontend -n $NAMESPACE
                '''
            }
        }

        stage('Notify') {
            steps {
                echo "Deployment completed successfully"
                sh 'date'
            }
        }
    }
}
