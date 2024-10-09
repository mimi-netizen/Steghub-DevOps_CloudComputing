# KUBERNETES ARCHITECTURE

Kubernetes is a not a single package application that you can install with one command, it is comprised of several components, some
of them can be deployed as services, some can be also deployed as separate containers.

Let us take a look at Kubernetes architecture diagram below:

![7003](https://user-images.githubusercontent.com/85270361/210190296-1aec0263-9b93-4f69-8bbf-4aaa13ec29fc.PNG)

Read about every component in the [official documentation](https://kubernetes.io/docs/concepts/overview/components/).

Make sure you understand the role of each component on the diagram above, without this understanding it will be extremely difficult
for you to install and operate a K8s cluster, especially when it comes to troubleshooting and maintenance.

As an IT professional in general, you shall be comfortable using official documentation for tools you use, in case of Kubernetes – it
has a very well structured and comprehensive documentation portal with multiple configuration code snippets. We strongly encourage
you to add it to your bookmarks and refer to it every time you havea K8s-related question.

## "Kubernetes From-Ground-Up"

K8s installation options

So far, Kubernetes sounds like a lot of fun, right? With its intuitive architecture, and rich configuration options, you may already
want to jump right in, spin up a few VMs and begin to install and configure a Kubernetes cluster. But hold on for a second. Installing
and configuring Kubernetes is far from being a walk in the park, i.e., it is very difficult to implement and get it ready for
production. Especially, if you want to setup a highly available, and secure Kubernetes cluster.

The good news is, there are open-source tools available today that already has all the hard work done and you can plug into them easily.
An example of that is [minikube](https://minikube.sigs.k8s.io/docs/start/), which can be used during testing and development.

For a better understanding of each aspect of spinning up a Kubernetes cluster, we will do it without any automated helpers. You
will install each and every component manually from scratch and learn how to make them work together – we call this approach "K8s
From-Ground-Up".

**To successfully implement "K8s From-Ground-Up", the following and even more will be done by you as a K8s administrator:**

1. Install and configure master (also known as control plane) components and worker nodes (or just nodes).

2. Apply security settings across the entire cluster (i.e., encrypting the data in transit, and at rest)

- In transit encryption means encrypting communications over the network using HTTPS
- At rest encryption means encrypting the data stored on a disk

3. Plan the capacity for the backend data store etcd

4. Configure network plugins for the containers to communicate

5. Manage periodical upgrade of the cluster

6. Configure observability and auditing

**Note:** Unless you have any business or compliance restrictions, ALWAYS consider to use managed versions of K8s – Platform as
a Service offerings, such as [Azure Kubernetes Service(AKS)](https://learn.microsoft.com/en-us/azure/aks/), Amazon Elastic Kubernetes
Service [(Amazon EKS)](https://aws.amazon.com/eks/), or [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine)
as they usually have better default security settings, and the costs for maintaining the control plane are very low.

You will be able to appreciate automation tools and managed versions of Kubernetes much more after you have experienced all the
lessons from the struggles and failures from the **"K8s From-Ground-Up"**.

Let us begin building out Kubernetes cluster from the ground
**DISCLAIMER:** The following setup of Kubernetes should be used for learning purpose only, and not to be considered for production.
This is because setting up a K8s cluster for production use has a lot more moving parts, especially when it comes to planning the
nodes, and securing the cluster. The purpose of **"K8s From-Ground-Up"** is to get you much closer to the different components as shown
in the architecture diagram and relate with what you have been learning about Kubernetes.

**Tools to be used and expected result of the Project 20**

- VM: AWS EC2
- OS: Ubuntu 20.04 lts+
- Docker Engine
- kubectl console utility
- cfssl and cfssljson utilities
- Kubernetes cluster

**You will create 3 EC2 Instances, and in the end, we will have the following parts of the cluster properly configured:**

- One Kubernetes Master
- Two Kubernetes Worker Nodes
- Configured SSL/TLS certificates for Kubernetes components to communicate securely
- Configured Node Network
- Configured Pod Network
