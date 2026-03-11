pipeline {
 agent any

 environment {
   ECR_REPO = "930664225702.dkr.ecr.us-east-1.amazonaws.com/hrms_ecr_repo"
 }

 stages {

 stage('Clone Code') {
   steps {
     git 'https://github.com/Poojamarne-123/hrms-sourcecode.git'
   }
 }

 stage('Build Docker Image') {
   steps {
     sh 'docker build -t hrms .'
   }
 }

 stage('Tag Image') {
   steps {
     sh 'docker tag hrms:latest $ECR_REPO:latest'
   }
 }

 stage('Push Image') {
   steps {
     sh 'docker push $ECR_REPO:latest'
   }
 }

 stage('Deploy to EKS') {
   steps {
     sh 'kubectl apply -f k8s/'
   }
 }

 }

}

