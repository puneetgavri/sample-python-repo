# GitHub Actions CI/CD Workflow for Backend Deployment

This directory contains GitHub Actions workflows for automated building and deployment of the backend service to Amazon EKS.

## Workflow: deploy-backend.yml

Automatically builds the backend Docker image, pushes it to Amazon ECR, and deploys it to an Amazon EKS cluster.

### Trigger
- Automatically runs on push to `main` branch

### Prerequisites

Before using this workflow, ensure you have:

1. **Amazon ECR Repository**: Create an ECR repository to store Docker images
2. **Amazon EKS Cluster**: Have an existing EKS cluster with a deployment named `backend-deployment`
3. **Kubernetes Deployment**: A deployment resource in your EKS cluster with:
   - Deployment name: `backend-deployment`
   - Container name: `backend`
4. **AWS IAM Permissions**: AWS credentials with permissions for:
   - ECR: Push images
   - EKS: Describe cluster, update kubeconfig
   - Kubernetes: Update deployments

### Required GitHub Secrets

Configure the following secrets in your GitHub repository settings (Settings → Secrets and variables → Actions):

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `AWS_ACCESS_KEY_ID` | AWS access key ID for authentication | `<AWS_ACCESS_KEY_ID>` |
| `AWS_SECRET_ACCESS_KEY` | AWS secret access key for authentication | `<AWS_SECRET_ACCESS_KEY>` |
| `AWS_REGION` | AWS region where ECR and EKS are located | `us-east-1` |
| `ECR_REPOSITORY` | Name of the ECR repository | `backend-service` |
| `EKS_CLUSTER_NAME` | Name of the EKS cluster | `production-cluster` |

### Workflow Steps

1. **Checkout code**: Retrieves the repository code
2. **Configure AWS credentials**: Authenticates with AWS using GitHub secrets
3. **Login to Amazon ECR**: Authenticates Docker with ECR
4. **Build, tag, and push image**: 
   - Builds Docker image using the Dockerfile in the repository root
   - Tags with commit SHA and `latest`
   - Pushes both tags to ECR
5. **Install kubectl**: Installs the latest stable kubectl CLI
6. **Configure kubectl for EKS**: Updates kubeconfig to connect to the EKS cluster
7. **Deploy to EKS**: Updates the deployment with the new image
8. **Verify deployment**: Checks deployment status and lists resources
9. **Deployment summary**: Displays success or failure message

### Image Tagging Strategy

- **Commit SHA**: Each build is tagged with the Git commit SHA for traceability
- **Latest**: The `latest` tag is updated with each successful build

### Kubernetes Deployment Name
The workflow assumes your Kubernetes deployment is named `backend-deployment` with a container named `backend`. If your deployment has a different name, update line 74 in the workflow file.
