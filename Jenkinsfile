pipeline {
    agent any
    
    environment {
        AWS_REGION = 'your_aws_region'
        ECR_REPO = 'your_ecr_repository_name'
        ECS_CLUSTER = 'your_ecs_cluster_name'
        ECS_SERVICE = 'your_ecs_service_name'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://$(AWS_REGION).dkr.ecr.$(AWS_REGION).amazonaws.com', 'ecr:us-east-1:AWS_ECR_CREDENTIAL_ID') {
                        def appImage = docker.build("$(AWS_REGION).dkr.ecr.$(AWS_REGION).amazonaws.com/${ECR_REPO}:${env.BUILD_NUMBER}")
                        appImage.push()
                    }
                }
            }
        }
        
        stage('Deploy to ECS') {
            steps {
                script {
                    sh '''
                        # Update ECS service with new task definition
                        aws ecs update-service --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} --force-new-deployment
                    '''
                }
            }
        }
    }
}
