# Deploying Applications Into Kubernetes Cluster

## Project Overview

This project explores the deployment of applications into a Kubernetes (K8s) cluster. It covers the following key areas:

1. **Deploying a Random Pod**: Learn how to deploy a basic Nginx container in a Kubernetes Pod and access it from within the cluster.

2. **Creating a Service**: Understand how to create a Kubernetes Service to provide a stable network endpoint for accessing the application running in a Pod.

3. **Exposing a Service on a Server's Public IP**: Learn how to expose a Kubernetes Service on a node's public IP address using the NodePort service type.

4. **Ensuring Desired Number of Pods**: Explore the use of Kubernetes ReplicaSets to maintain a stable set of Pod replicas running at all times.

5. **Scaling ReplicaSets Up and Down**: Understand how to scale ReplicaSets in both an imperative and declarative manner.

6. **Advanced Label Matching**: Discuss more advanced label matching capabilities in Kubernetes.

## Key Concepts Covered

- Kubernetes Pods
- Kubernetes Services (ClusterIP, NodePort)
- Kubernetes ReplicaSets
- Kubernetes object management (imperative and declarative)
- Kubernetes label selectors

## Prerequisites

- Basic understanding of containers and Docker
- Familiarity with Kubernetes concepts and architecture
- Access to a Kubernetes cluster (e.g., minikube, kind, or a cloud-hosted cluster like EKS, GKE, AKS)
- Kubectl command-line tool installed and configured to interact with the Kubernetes cluster

## Getting Started

1. Clone the project repository: `git clone https://github.com/mimi-netizen/Steghub-DevOps_CloudComputing.git`
2. Navigate to the project directory: `cd Steghub-DevOps_CloudComputing/Project 22 Deploying Applications Into Kubernetes Cluster`
3. Follow the step-by-step instructions in the `project.md` file to explore the different aspects of deploying applications into a Kubernetes cluster.

## Conclusion

By completing this project, you will gain a deeper understanding of how to effectively deploy and manage applications in a Kubernetes environment. The concepts and techniques covered will equip you with the necessary knowledge to build and operate robust, scalable, and highly available applications on top of a Kubernetes infrastructure.
