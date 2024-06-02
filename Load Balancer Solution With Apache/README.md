# Tooling Website Solution with Load Balancer

## Project Overview

This project aims to deploy and configure an Apache Load Balancer to distribute traffic across two web servers hosting the Tooling Website solution.

## Prerequisites

Before proceeding with this task, ensure that you have the following components already configured:

1. Two RHEL8 Web Servers
2. One MySQL DB Server (Ubuntu 20.04)
3. One RHEL8 NFS Server

The overall architecture of the project is depicted in the diagram below:

![6007](https://user-images.githubusercontent.com/85270361/210140264-3d8cb37c-d631-4a16-bbeb-22e8e172595e.PNG)

## Task Objectives

The main objectives of this task are:

1. Deploy an Ubuntu EC2 instance to host the Apache Load Balancer.
2. Install and configure the Apache Load Balancer on the Ubuntu EC2 instance.
3. Configure the Load Balancer to distribute traffic across the two web servers.
4. Ensure that users can access the Tooling Website solution through the Load Balancer.

## Implementation Steps

1. **Launch an Ubuntu EC2 Instance for the Load Balancer**: - Create a new EC2 instance using the Ubuntu 20.04 AMI. - Configure the necessary security groups and network settings to allow access to the Load Balancer.

2. **Install and Configure the Apache Load Balancer**:

- Connect to the Ubuntu EC2 instance and update the package index:

  ```powershell
  sudo apt update
  ```

- Install the Apache Load Balancer package:

  ```powershell
  sudo apt install apache2 -y
  ```

- Configure the Load Balancer by modifying the `/etc/apache2/sites-available/000-default.conf` file:

  ```powershell
  sudo vi /etc/apache2/sites-available/000-default.conf
  ```

- Add the following configuration block within the `<VirtualHost *:80>` section:

  ```powershell
  <Proxy balancer://mycluster>
      BalancerMember http://<Web-Server-1-Public-IP-Address>
      BalancerMember http://<Web-Server-2-Public-IP-Address>
      ProxySet lbmethod=byec2
  </Proxy>

  ProxyPreserveHost On
  ProxyPass / balancer://mycluster/
  ProxyPassReverse / balancer://mycluster/
  ```

- Save and close the file.

- Restart the Apache service:

  ```powershell
  sudo systemctl restart apache2
  ```

3. **Verify the Load Balancer Configuration**: - Open a web browser and access the Load Balancer's public IP address.

- Ensure that the Tooling Website is accessible and that the load is being distributed across the two web servers.
