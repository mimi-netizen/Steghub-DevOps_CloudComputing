# Deploying and Packaging Applications into Kubernetes with Helm

This project demonstrates how to deploy various DevOps tools into Kubernetes using Helm package manager and configure proper ingress routing.

## Tools Covered

- Artifactory (JFrog)
- Hashicorp Vault
- Prometheus
- Grafana
- Elasticsearch ELK using ECK

## Key Concepts Implemented

- Helm chart deployments
- Kubernetes ingress configuration
- Load balancer setup
- DNS configuration with Route53
- Private registry setup
- Security scanning for artifacts

## Getting Started

### Prerequisites

- Kubernetes cluster
- Helm installed
- kubectl CLI
- AWS account (for Route53 and Load Balancer)

### Installation Steps

1. Create namespace for tools:

```bash
kubectl create ns tools
```

2. Add JFrog Helm repository:

```bash
helm repo add jfrog https://charts.jfrog.io
helm repo update
```

3. Install Artifactory:

```bash
helm upgrade --install artifactory jfrog/artifactory -n tools
```

4. Deploy Nginx Ingress Controller:

```bash
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

5. Configure ingress rules for routing
6. Set up DNS records in Route53
7. Configure security groups and networking

## Configuration

- The project uses nginx ingress controller for routing traffic
- DNS configuration uses either CNAME or AWS Alias records
- Default credentials:
  - Username: admin
  - Password: password

## Security Considerations

- Private registry setup for docker images and helm charts
- Artifact scanning capabilities
- Corporate security policy compliance
- SSL/TLS termination at ingress level

## Notes

- Always specify helm chart versions explicitly
- Configure proper security groups and networking rules
- Follow cloud provider best practices
- Consider cost implications of load balancer usage

## Additional Resources

- [Artifactory Documentation](https://www.jfrog.com/confluence/display/JFROG/JFrog+Artifactory)
- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

This project is part of a larger DevOps infrastructure setup focusing on deployment automation and package management in Kubernetes environments.
