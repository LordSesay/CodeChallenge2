# Continuous Deployment with Docker, Jenkins, and AWS EKS

This project demonstrates the process of deploying a simple web application using Docker, Jenkins, and AWS Elastic Kubernetes Service (EKS). The project involves:

- Creating a simple "Hello, World!" web application.
- Dockerizing the application and deploying it to an AWS EKS cluster using Kubernetes.
- Automating the deployment pipeline with Jenkins.
- Utilizing Terraform to provision AWS resources such as EKS and ECR.

The application is continuously built and deployed using Jenkins CI/CD pipelines.
## Setting Up the Environment

Before getting started, ensure you have the following installed and configured:

### Prerequisites

- **AWS Account**: You’ll need an active AWS account to provision EKS and ECR.
- **AWS CLI**: Install and configure the AWS CLI with `aws configure`.
- **Terraform**: Version 1.x (installed and initialized).
- **Docker**: To build the application image.
- **kubectl**: For managing the EKS cluster.
- **Jenkins**: To set up the CI/CD pipeline.

To install these tools:
- [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)
- [Install Docker](https://docs.docker.com/get-docker/)
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Install Jenkins](https://www.jenkins.io/doc/book/installing/)

### AWS Configuration

Make sure your AWS CLI is configured with appropriate access:
```bash
aws configure

---

### **3. Deploying the Application (Deployment Steps)**
This section explains how to deploy the application, broken into clear steps.

```markdown
## Deploying the Application

### Step 1: Clone the Repository
Clone this GitHub repository to your local machine:
```bash
git clone https://github.com/your-username/your-repository-name.git
cd your-repository-name
Provision the AWS Infrastructure with Terraform
Initialize Terraform:
Run the following command to initialize Terraform and download the necessary plugins.
bash
Copy code
terraform init
Apply the Terraform Configuration:
Apply the configuration to provision AWS resources (EKS, ECR, VPC, etc.). Terraform will ask for approval before provisioning.
bash
Copy code
terraform apply
Configure kubectl for EKS:
Once the infrastructure is provisioned, configure your kubectl to interact with the EKS cluster:
bash
Copy code
aws eks --region <region> update-kubeconfig --name <cluster-name>
Build and Push the Docker Image
Ensure your Docker image is properly built and pushed to the ECR repository. You can manually do this or automate it using Jenkins.

bash
Copy code
docker build -t <aws_account_id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest .
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest
Step 4: Deploy the Application to EKS
Once the Docker image is pushed to ECR, use kubectl to deploy the application to the EKS cluster:

bash
Copy code
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
This will deploy the application and expose it via a LoadBalancer.

yaml
Copy code

---

### **4. Explanation of Terraform Code**
In this section, explain the purpose of each resource in your Terraform configuration file. Break down how the Terraform script provisions AWS infrastructure like EKS and ECR.

```markdown
## Terraform Code Explanation

The Terraform configuration (`main.tf`) provisions the following resources:

### EKS Cluster
The EKS cluster is created with the following resources:
```hcl
resource "aws_eks_cluster" "my_cluster" {
  name = "my-cluster"
  role_arn = <role_arn>

  vpc_config {
    subnet_ids = <subnet_ids>
  }
}
vpc_config {
    subnet_ids = <subnet_ids>
  }
}
Explanation: This block provisions an EKS cluster with a given name and assigns it to a VPC. The subnet_ids are provided by the VPC resource, which will be set up in another part of the Terraform configuration.

ECR Repository
This resource creates an Amazon ECR repository to store Docker images:

hcl
Copy code
resource "aws_ecr_repository" "my_app_repo" {
  name = "my-app-repo"
}
VPC and Networking Resources
hcl
Copy code
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}
Explanation: This block provisions a VPC with the specified CIDR block.

yaml
Copy code

---

### **5. Explanation of Jenkins Pipeline**
Explain how the Jenkins pipeline automates the building, pushing, and deploying process. You should break down the pipeline script and provide comments.

```markdown
## Jenkins Pipeline Explanation

The Jenkins pipeline consists of the following stages:

### 1. Build Docker Image
This stage builds the Docker image using the Dockerfile and tags it with the latest version.
```groovy
stage('Build Docker Image') {
    steps {
        script {
            docker.build('my-app', '.')
        }
    }
}
2. Push Image to ECR
This stage logs into Amazon ECR and pushes the Docker image to the ECR repository.

groovy
Copy code
stage('Push to ECR') {
    steps {
        script {
            docker.withRegistry('https://<aws_account_id>.dkr.ecr.<region>.amazonaws.com', 'ecr:aws-credentials') {
                docker.image('my-app').push('latest')
            }
        }
    }
}
3. Deploy to EKS
This stage deploys the Docker image to the EKS cluster using kubectl.

groovy
Copy code
stage('Deploy to EKS') {
    steps {
        script {
            sh 'kubectl apply -f kubernetes/deployment.yaml'
            sh 'kubectl apply -f kubernetes/service.yaml'
        }
    }
}
yaml
Copy code

---

### **6. URL of Deployed Application**
In this section, you would provide the URL to your deployed application. This is typically the external IP or LoadBalancer URL exposed by your service.

```markdown
## Deployed Application

The deployed application can be accessed at the following URL:

[http://<load-balancer-ip-or-dns-name>](http://<load-balancer-ip-or-dns-name>)
7. Conclusion and Additional Notes
Wrap up the documentation with any final remarks, such as next steps, potential improvements, or additional notes for reviewers.

markdown
Copy code
## Conclusion

This project demonstrates how to deploy a simple web application using Docker, Jenkins, and AWS EKS. The CI/CD pipeline ensures that updates to the Docker image are automatically pushed to the ECR and deployed to the EKS cluster. The infrastructure is provisioned using Terraform for automation and repeatability.

Feel free to extend this setup for more complex applications and workflows.
