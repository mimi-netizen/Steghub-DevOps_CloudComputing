# Building EKS Cluster with Terraform and Deploying Applications using Helm

This project demonstrates how to set up an Amazon EKS (Elastic Kubernetes Service) cluster using Terraform and deploy applications using Helm.

## Project Overview

This project covers:

1. Building an EKS cluster using Terraform
2. Deploying applications using Helm
3. Setting up Jenkins on EKS using Helm

## Prerequisites

- AWS CLI configured with appropriate credentials
- Terraform installed
- kubectl installed
- Helm installed
- eksctl installed

## Project Structure

```hcl
.
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── helm-files/
│   ├── jenkins-values.yaml
│   └── application-values.yaml
└── README.md
```

## Setup Instructions

### 1. EKS Cluster Creation with Terraform

1. Initialize Terraform:

```bash
terraform init
```

2. Review the planned changes:

```bash
terraform plan
```

3. Apply the configuration:

```bash
terraform apply
```

4. Configure kubectl:

```bash
aws eks update-kubeconfig --region us-west-2 --name eks-cluster-name
```

### 2. Application Deployment with Helm

1. Add required Helm repositories:

```bash
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

2. Deploy applications using Helm:

```bash
helm install [release-name] [chart] -f values.yaml
```

### 3. Jenkins Deployment

1. Add Jenkins Helm repository:

```bash
helm repo add jenkins https://charts.jenkins.io
helm repo update
```

2. Install Jenkins:

```bash
helm install jenkins jenkins/jenkins -f jenkins-values.yaml
```

## Configuration Files

- `terraform/` - Contains all Terraform configuration files for EKS cluster setup
- `helm-files/` - Contains Helm values files for application deployments

## Important Notes

- Ensure proper IAM permissions are configured
- Review and modify security group settings as needed
- Follow AWS best practices for EKS cluster configuration
- Regularly update and maintain Kubernetes components

## Clean Up

To destroy the infrastructure:

```bash
terraform destroy
```

## Contributing

Feel free to contribute to this project by submitting pull requests or creating issues for any bugs or improvements.

## Security

- Ensure to follow security best practices
- Keep all components updated
- Review and modify default configurations as per your security requirements

## License

This project is licensed under the MIT License - see the LICENSE file for details.
