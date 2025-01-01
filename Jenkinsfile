pipeline {
    agent any  // Use any available Jenkins agent

    environment {
        // Define environment variables
        ECR_REPO = "aws_account_id.dkr.ecr.region.amazonaws.com/repo_name" // Replace with your ECR repo URL
        AWS_REGION = "us-west-2" // Set the AWS region
        EKS_CLUSTER_NAME = "my-eks-cluster" // Set your EKS cluster name
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the GitHub repository
                git 'https://github.com/your-repo-url.git' // Replace with your repository URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile in the repo
                    docker.build("my-app:latest")  // Use your Docker image name
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    // Authenticate Docker with Amazon ECR
                    sh '$(aws ecr get-login --no-include-email --region $AWS_REGION)'

                    // Tag the Docker image with the ECR repository URL
                    sh 'docker tag my-app:latest $ECR_REPO:latest'

                    // Push the Docker image to ECR
                    sh 'docker push $ECR_REPO:latest'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // Set up AWS credentials and configure kubectl to use the EKS cluster
                    sh 'aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTER_NAME'

                    // Deploy to EKS using kubectl
                    sh 'kubectl apply -f kubernetes/deployment.yaml' // Ensure your deployment.yaml is in the repo
                }
            }
        }
    }

    post {
        success {
            // Clean up or notify when the build is successful
            echo "Build and deployment successful!"
        }
        failure {
            // Handle failure scenarios, like sending notifications
            echo "Build or deployment failed."
        }
    }
}
