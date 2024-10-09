# INTRODUCTION

In previous Project 20 you have started to work with containerization and have learned how to prepare and deploy a Docker container
using Docker Compose.

In this project, you will continue building upon your containerization skills, and begin to work on industry tools that fit for
production deployment.

Containers are the most lightweight and easily transferrable workloads, they start faster than VMs, consume less space and memory and,
they perfectly fit to accommodate microservice architecture. It means that number of containers will be usually significantly higher
that number of VMs and with number comes complexity of managing containers.

To manage a fleet of containers you can use different software solutions, but the most widely used one is Kubernetes
(also referred as K8s). It was originally developed by Google and now being maintained by Cloud Native Computing Foundation (CNCF).

Why has it become so popular? It is a powerful Swiss-army knife when it comes to managing multiple containers in production-grade
solutions.

For now we will not list down all the features and benefits of Kubernetes, you can watch this overview video to get to know more,
but you certainly must have this tool in your DevOps arsenal and know when and how to use if for production-grade deployments.

This project is the first of a series Kubernetes-related practice projects, so get ready to become a professional containers’
fleet "pilot"\*.

## \*Kubernetes (κυβερνήτης, Greek for "helmsman" or "pilot" or "governor", and the etymological root of cybernetics)

![7004](https://user-images.githubusercontent.com/85270361/210185828-a3495b48-13f8-423e-8376-5c97307fa1f3.PNG)

## Why migrate from Docker Compose to K8s

In the previous project you successfully deployed your Docker containers using Docker Compose, it is a great tool that helps avoiding
execution of multiple CLI commands by preparing a declarative configuration file. It is handy when you deploy one or a few containers,
but in most cases, it does not fit for production deployments.

Because of the many limitations that Docker Compose has, it is very important for us to consider migrating our solution to more an
advanced technology. The most common alternatives to Compose, amongst a few others, are Docker Swarm and Kubernetes.

## What is wrong with Docker Compose?

It is important to understand that, DevOps is about "Culture" and NOT "Tools" Therefore, it is not correct to say that one tool is
better than another; different organizations have different needs and a good tool for one team may be bad for another just because
their needs are not the same. In some teams, Docker Compose fit their needs perfectly, despite the perceived limitations. The major
limitation of Docker Compose is that it can only be used to run workloads on a single computer host. Now, that is an obvious
limitation because if our Tooling Application and its MySQL Database are all running on a single VM, like we did in Project 20,
then this host is considered as a SPOF (i.e. – Single Point Of Failure).

So, could we say there is something wrong with Docker Compose? Not exactly, as a matter of fact, it is being used a lot in the
industry. It fits well into some use cases that require speedy development and Proof of Concepts. As you will soon see, Kubernetes
is a lot more complex technology, and it may be an overkill for some use cases.

## Container Orchestration with Kubernetes

**What is Container Orchestration?**

Two important things to remember about Docker containers are:

1. Unlike virtual machines, they are not designed to run for a very long time. By design, Docker containers are ephemeral. By “ephemeral”
   it means that a container can be stopped and destroyed, and a new one can be built from the same Docker image and put in place with
   an absolute minimum set-up and configuration requirement.

2. To ensure that container workloads are highly scalable, they must be configured to run across multiple compute nodes. A compute node
   can be a physical server or a virtual machine with Docker engine installed to run your containers.

If we had two compute nodes to run our containers, let us consider a following scenarios:

1. Given the two points mentioned above, if containers are configured to run across 2 computer nodes, and a particular container,
   running on Node 1 dies, how will it know that it can spin up again on Node 2?

2. Let us imagine that Tooling website container is running on Node 1, and MySQL container is running on Node 2, how will both containers
   be able to communicate with each other? Remember in Project 20, we had to create a custom network on the same host and ensure that
   they can communicate through that network. But in the case of 2 separate hosts, this is natively not possible.

**Container orchestration** is a concept that allows to address these two scenarios, it provides automation of all the aspects of
coordinating and managing containers. Container orchestration is focused on managing life cycle of containers and their dynamic
environments.

It is about automating the entire lifecycle of containers running across multiple nodes:

- Configuring and scheduling of containers on nodes
- Ensuring the availability of containers, even when they die
- Scaling of containers to equally balance application workloads across infrastructure
- Allocation of resources between containers
- Load balancing, traffic routing and service discovery of containers
- Health monitoring of containers
- Securing the interactions between containers.

Kubernetes is a tool designed to do Container Orchestration and it does its job very well when correctly configured.

As mentioned earlier, there are other alternatives to **Docker Compose**. But, throughout the entire PBL program, we will not focus
on **Docker Swarm**. We will rather spend more time with **Kubernetes**. Part of the reason for this is because Kubernetes has more
functionalities and is widely in use in the industry.

To know when to choose between Docker Swarm and Kubernetes,
[Here is an interesting article to read](https://dzone.com/articles/quotdocker-swarm-or-kubernetesquot-is-it-the-right)
with some very enlightening stats.
