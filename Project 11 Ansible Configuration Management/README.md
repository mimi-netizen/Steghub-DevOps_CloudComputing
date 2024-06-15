# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 7 TO 10

You have been implementing some interesting projects up untill now, and that is awesome.

In Projects 7 to 10 you had to perform a lot of manual operations to seet up virtual servers, install and configure required software,
deploy your web application.

This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration
Management, at the same time you will become confident at writing code using declarative language such as YAML.

Let us get started!

Instructions On How To Submit Your Work For Review And Feedback
To submit your work for review and feedback – follow this instruction.

Ansible Client as a Jump Server (Bastion Host)
A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be
provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network
which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and
can only access it through a Jump Server – it provide better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private
subnet is only reachable by private IP addresses.

![6033](https://user-images.githubusercontent.com/85270361/210153615-ea6cf398-05d3-45d0-9ea4-6daffac7fa4c.PNG)

When you reach Project 15, you will see a Bastion host in proper action. But for now, we will develop Ansible scripts to simulate
the use of a Jump box/Bastion host to access our Web Servers.

## Task

1. Install and configure Ansible client to act as a Jump Server/Bastion Host
2. Create a simple Ansible playbook to automate servers configuration
