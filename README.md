
# Continuous Deployment with Docker, Jenkins, and AWS EKS

This project demonstrates the process of deploying a simple web application using Docker, Jenkins, and AWS Elastic Kubernetes Service (EKS). The project involves:

- Creating a simple "Hello, World!" web application.
- Dockerizing the application and deploying it to an AWS EKS cluster using Kubernetes.
- Automating the deployment pipeline with Jenkins.
- Utilizing Terraform to provision AWS resources such as EKS and ECR.
- Continuously building and deploying the application using Jenkins CI/CD pipelines.

## Table of Contents

1. [Setting Up the Environment](#setting-up-the-environment)
2. [Deploying the Application](#deploying-the-application)
3. [Explanation of Terraform Code](#explanation-of-terraform-code)
4. [Explanation of Jenkins Pipeline](#explanation-of-jenkins-pipeline)
5. [URL of Deployed Application](#url-of-deployed-application)
6. [Conclusion and Additional Notes](#conclusion-and-additional-notes)

---

## Setting Up the Environment

Before getting started, ensure you have the following installed and configured:

### Prerequisites

- **AWS Account**: You’ll need an active AWS account to provision EKS and ECR.
- **AWS CLI**: Install and configure the AWS CLI with `aws configure`.
- **Terraform**: Version 1.x (installed and initialized).
- **Docker**: To build the application image.
- **kubectl**: For managing the EKS cluster.
- **Jenkins**: To set up the CI/CD pipeline.

### Installation Steps

1. **Install AWS CLI**
2. **Install Terraform**
3. **Install Docker**
4. **Install kubectl**
5. **Install Jenkins**

### AWS Configuration

Make sure your AWS CLI is configured with appropriate access:

```bash
aws configure
```

---

## Deploying the Application

This section explains how to deploy the application, broken into clear steps.

### Step 1: Clone the Repository

Clone this GitHub repository to your local machine:

```bash
git clone https://github.com/your-username/your-repository-name.git
cd your-repository-name
```

### Step 2: Provision the AWS Infrastructure with Terraform

#### Initialize Terraform

Run the following command to initialize Terraform and download the necessary plugins:

```bash
terraform init
```

#### Apply the Terraform Configuration

Apply the configuration to provision AWS resources (EKS, ECR, VPC, etc.). Terraform will ask for approval before provisioning:

```bash
terraform apply
```

### Step 3: Configure kubectl for EKS

Once the infrastructure is provisioned, configure your kubectl to interact with the EKS cluster:

```bash
aws eks --region <region> update-kubeconfig --name <cluster-name>
```

### Step 4: Build and Push the Docker Image

Build the Docker image and push it to the ECR repository. You can manually do this or automate it using Jenkins:

```bash
docker build -t <aws_account_id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest .
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest
```

### Step 5: Deploy the Application to EKS

Once the Docker image is pushed to ECR, use kubectl to deploy the application to the EKS cluster:

```bash
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

This will deploy the application and expose it via a LoadBalancer.

---

## Explanation of Terraform Code

The Terraform configuration (`main.tf`) provisions the following resources:

### EKS Cluster

```hcl
resource "aws_eks_cluster" "my_cluster" {
  name = "my-cluster"
  role_arn = <role_arn>

  vpc_config {
    subnet_ids = <subnet_ids>
  }
}
```

**Explanation**: This block provisions an EKS cluster with a given name and assigns it to a VPC. The subnet IDs are provided by the VPC resource.

### ECR Repository

This resource creates an Amazon ECR repository to store Docker images:

```hcl
resource "aws_ecr_repository" "my_app_repo" {
  name = "my-app-repo"
}
```

### VPC and Networking Resources

```hcl
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}
```

**Explanation**: This block provisions a VPC with the specified CIDR block.

---

## Explanation of Jenkins Pipeline

The Jenkins pipeline automates the following steps:

### 1. Build Docker Image

This stage builds the Docker image using the Dockerfile and tags it with the latest version:

```groovy
stage('Build Docker Image') {
    steps {
        script {
            docker.build('my-app', '.')
        }
    }
}
```

### 2. Push Image to ECR

This stage logs into Amazon ECR and pushes the Docker image:

```groovy
stage('Push to ECR') {
    steps {
        script {
            docker.withRegistry('https://<aws_account_id>.dkr.ecr.<region>.amazonaws.com', 'ecr:aws-credentials') {
                docker.image('my-app').push('latest')
            }
        }
    }
}
```

### 3. Deploy to EKS

This stage deploys the Docker image to the EKS cluster using kubectl:

```groovy
stage('Deploy to EKS') {
    steps {
        script {
            sh 'kubectl apply -f kubernetes/deployment.yaml'
            sh 'kubectl apply -f kubernetes/service.yaml'
        }
    }
}
```

---

## URL of Deployed Application

The deployed application can be accessed at the following URL:

[http://<load-balancer-ip-or-dns-name>](http://<load-balancer-ip-or-dns-name>)

---

## Conclusion and Additional Notes

This project demonstrates how to deploy a simple web application using Docker, Jenkins, and AWS EKS. The CI/CD pipeline ensures that updates to the Docker image are automatically pushed to ECR and deployed to the EKS cluster. The infrastructure is provisioned using Terraform for automation and repeatability.

Feel free to extend this setup for more complex applications and workflows.
