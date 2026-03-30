pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_BACKEND = '930664225702.dkr.ecr.us-east-1.amazonaws.com/hrms-backend'
        ECR_FRONTEND = '930664225702.dkr.ecr.us-east-1.amazonaws.com/hrms-frontend'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/Poojamarne-123/hrms-sourcecode.git'
            }
        }

        stage('Docker Login to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin ${ECR_BACKEND}
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin ${ECR_FRONTEND}
                """
            }
        }

        stage('Build Docker Images') {
            steps {
                sh """
                docker build -t hrms-backend backend/
                docker build -t hrms-frontend frontend/
                """
            }
        }

        stage('Tag Docker Images') {
            steps {
                sh """
                docker tag hrms-backend:latest ${ECR_BACKEND}:latest
                docker tag hrms-frontend:latest ${ECR_FRONTEND}:latest
                """
            }
        }

        stage('Push Docker Images to ECR') {
            steps {
                sh """
                docker push ${ECR_BACKEND}:latest
                docker push ${ECR_FRONTEND}:latest
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                # Apply Kubernetes manifests
                kubectl apply -f k8s/deployment.yaml
		kubectl apply -f k8s/frontend_deployment.yaml
                kubectl apply -f k8s/service.yaml
		kubectl apply -f k8s/frontend_service.yaml
                kubectl apply -f k8s/ingress.yaml
                kubectl apply -f k8s/hpa.yaml
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                kubectl get pods
                kubectl get svc
                kubectl get ingress
                kubectl get hpa
                """
            }
        }
    }
}
