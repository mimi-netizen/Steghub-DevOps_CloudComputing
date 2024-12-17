# Streamlining Tooling Application Deployments with Helm, Kustomize, and Vault

## Project Overview

This project demonstrates how to efficiently deploy and manage a tooling application in Kubernetes using Helm, Kustomize, and HashiCorp Vault. It showcases modern DevOps practices for managing deployments across multiple environments (dev, sit, prod) while maintaining secure secrets management.

## Table of Contents

1. [Key Goals](#key-goals)
2. [Technologies Used](#technologies-used)
3. [Project Structure](#project-structure)
4. [Deployment Methods](#deployment-methods)
5. [Kustomize Implementation](#kustomize-implementation)
6. [Environment Configurations](#environment-configurations)
7. [Aurora Database Integration](#aurora-database-integration)
8. [Setup Instructions](#setup-instructions)

## Key Goals

- **Efficient Deployments**: Utilize Helm for package management and Kustomize for environment-specific configurations
- **Secure Secrets Management**: Implement HashiCorp Vault for secure credential storage
- **Environment Management**: Configure and manage multiple environments (dev, sit, prod)
- **Database Integration**: Set up and integrate Amazon Aurora with the application

## Technologies Used

- Kubernetes
- Helm
- Kustomize
- HashiCorp Vault
- Amazon Aurora
- Terraform
- Docker

## Project Structure

```plaintext
├── tooling-app-kustomize/
│   ├── base/
│   │   ├── deployment.yaml
│   │   ├── kustomization.yaml
│   │   └── service.yaml
│   └── overlays/
│       ├── dev/
│       ├── sit/
│       └── prod/
└── rds_aurora/
    ├── main.tf
    ├── provider.tf
    ├── data.tf
    ├── variables.tf
    └── output.tf
```

## Deployment Methods

### 1. Direct kubectl Deployment

- Basic deployment using `kubectl apply -f <manifest-file-path>`
- Suitable for development and testing
- Not recommended for production environments

### 2. Helm Deployment

- Package management for Kubernetes applications
- Templating engine for configuration
- Best suited for community components

### 3. Kustomize Overlays

- Native Kubernetes configuration management
- Environment-specific configurations
- Patch-based customization

## Kustomize Implementation

### Base Configuration

Contains common configurations shared across all environments:

- Deployment specifications
- Service definitions
- Resource limits

### Environment Overlays

Environment-specific configurations for:

- Namespace definitions
- Resource adjustments
- Image versions
- Replica counts

## Environment Configurations

### Dev Environment

- Basic configuration
- Minimal resource allocation
- 3 replicas

### SIT Environment

- Enhanced resource allocation
- 5 replicas
- Higher CPU and memory limits

### Production Environment

- Maximum resource allocation
- 4 replicas
- Production-grade configurations

## Aurora Database Integration

### Infrastructure Setup

- Terraform-managed Aurora cluster
- High availability configuration
- Secure networking setup

### Security Features

- VPC security groups
- Subnet isolation
- Encrypted storage
- Access control

## Setup Instructions

1. Prerequisites Installation:

```bash
# Install kubectl
# Install Kustomize
# Install Helm
# Install Terraform
```

2. Deploy Base Infrastructure:

```bash
cd rds_aurora
terraform init
terraform plan
terraform apply
```

3. Deploy Application Environments:

```bash
# Deploy Dev
kubectl apply -k overlays/dev

# Deploy SIT
kubectl apply -k overlays/sit

# Deploy Prod
kubectl apply -k overlays/prod
```

## Best Practices

1. **Configuration Management**

   - Use version control for all configurations
   - Implement environment-specific overlays
   - Maintain clear separation of concerns

2. **Security**

   - Implement least privilege access
   - Use secrets management
   - Regular security audits

3. **Resource Management**
   - Implement resource quotas
   - Monitor resource usage
   - Scale based on demand

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
