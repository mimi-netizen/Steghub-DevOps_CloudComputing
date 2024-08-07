# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

## INTRODUCTION

This project demostrates how a secure infrastructure inside AWS VPC (Virtual Private Cloud) network is built for a particular company, who uses [WordPress CMS](https://wordpress.com/) for its main business website, and a [Tooling Website](https://github.com/mimi-netizen/tooling.git) for their DevOps team. As part of the company’s desire for improved security and performance, a decision has been made to use a [reverse proxy technology from NGINX](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) to achieve this. The infrastructure will look like following diagram:

![](image/architecture.png)

# Starting Off Your AWS Cloud Project

## Set Up a Sub-account And Create A Hosted Zone

There are few requirements that must be met before you begin:

1. Properly configure your AWS account and Organization Unit [Watch How To Do This Here](https://www.youtube.com/watch?v=9PQYCc_20-Q)

   - Create an `AWS Master account`. (Also known as Root Account)

     Go to AWS console, and navigate to Services > All Services > Management & Governance > AWS Organizations

     If you haven't already created an organization, click "Create an organization" and select "Enable All Features".

   ![](image/org.jpg)

   - Within the Root account, create a sub-account and name it `DevOps`. (You will need another email address to complete this)

     Navigate to Add an AWS accout > Fill in the required details:

     - Account name: DevOps

     - Email address: Provide a new email address (different from the root account). 1.6. Click "Create". AWS will send a verification email to the provided email address.

     - Verify the email address by following the instructions in the email sent by AWS.

   ![](image/org1.jpg)

   ![](image/org2.jpg)

   - Within the Root account, create an `AWS Organization Unit (OU)`. Name it `Dev`. (We will launch Dev resources in there)

     From the AWS Organization page, Click on root > Action > Create new

   ![](image/org3.jpg)

   - Move the `DevOps` account into the `Dev OU`.

     Select the account to move, then Click Action > Move, then select the OU to move the account to and click move AWS account.

   ![](image/org4.jpg)

   ![](image/org5.jpg)

   - Login to the newly created AWS account using the new email address.

     - Go to the AWS Management Console login page.
     - Enter the email address used for the DevOps account and click Next.
     - Click on "Forgot your password?" if you don't have the initial password.
     - Follow the instructions to reset the password and Log in with the new password

   ![](image/org6.jpg)

2. Create a free domain name for your fictitious company at Freenom domain registrar here. We use [cloudns](https://www.cloudns.net) instead.

![](image/dns.jpg)

3. Create a hosted zone in AWS, and map it to your free domain from Freenom. Watch how to do that here

   - In the search bar, type Route 53 and select Route 53 from the results. > In the Route 53 dashboard, click on Hosted zones in the left-hand navigation pane > Click Create hosted zone Fill in the following details:

     - Domain name: Enter your Freenom domain name (e.g., example.tk).
     - Comment: Optional.
     - Type: Select Public hosted zone.
     - VPC: Leave this blank for a public hosted zone.
     - Click Create hosted zone

![](image/hosted-zone.jpg)

![](image/hosted.jpg)

![](image/hosted1.jpg)

![](image/hosted2.jpg)

- Let's map the hosted zone to our free domain

Copy the name server (NS) values from AWS, then go to your free domain, edit the default `NS` values and update them with the values from AWS.

![](image/ns.jpg)

The next step is to get a certificate from `AWS Certificate Manager`. The reason we are creating a certificate first is because when creating `ALB` we need to select a certificate.

- Click on request a Cert > Request public cert > Next

![](image/cert.jpg)

![](image/cert1.jpg)

In the domain name, we are going to use a wild card i.e (\*.). should in case we want to have another `name` or `subdomain`, the `WILDCARD` will make sure that any name before the domain name is attached to the `certificate`. e.g kydd.cdk-aws.dns-dynamic.net

![](image/cdk.jpg)

NOTE: Because we are using DNS verification is going to automatically write to the Rout53 to confirm

![](image/cer.jpg)

**_NOTE : As you proceed with configuration, ensure that all resources are appropriately tagged, for example:_**

Project: Give your project a name `Project-15`

Environment: `dev`

Automated:`No` (If you create a recource using an automation tool, it would be Yes)

## Set Up a Virtual Private Network (VPC)

Always make reference to the architectural diagram and ensure that your configuration is aligned with it.

### 1. Create a [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html).

![vpc](./images/create-vpc.png)

### Enable DNS hostnames.

Actions > Edit VPC Settings > Enable DNS hostnames

![](./images/enable-dns-hostname.png)

### 2. Create subnets as shown in the architecture

Create public and private subnets in each availablity zones respectively. Thus, we create public subnet in availability zone A and B respectively. We create 4 private subnets with respect to the diagram we are working with as such we create 2 private subnets each in availability zone A and B.

![](./images/subnet1.png)
![](./images/subnet2.png)
![](./images/subnet3.png)
![](./images/subnet4.png)
![](./images/subnet5.png)
![](./images/subnet6.png)

Enable Auto-assign public IPv4 address in the public subnets.
Actions > Edit subnet settings > Enable auto-assign public IPv4

![](./images/en-auto-assign-pubIP.png)

All Subnets

![](./images/all-subnets.png)

### 3. Create a route table and associate it with public subnets

Create a public route table for the public subnets

![](./images/create-pub-rt.png)

Associate it with public subnets.
Click on the route table > Subnet Association > Edit Subnet Association.

![](./images/ass-pub-rt-subnets.png)

The public route table and it's associated public subnets

![](./images/pub-rt.png)

### 4. Create a route table and associate it with private subnets

Create a private route table for the private subnets

![](./images/create-private-rt.png)

Associate it with private subnets

![](./images/ass-privt-rt-subnets.png)

The route tables

![](./images/rout-tables.png)

### 5. Create an [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)

![](./images/create-igw.png)

Attach it to the created VPC.

![](./images/attach-igw.png)
![](./images/igw-attached.png)

### 6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)

In the public route table, Click route tab > Edit route > Add route
since we are routing traffic to the internet, the destination will be `0.0.0.0/0`

![](./images/route-pub-rt.png)

![](./images/pub-route.png)

### 7. Create 3 [Elastic IPs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

![](./images/create-eip.png)

### 8. Create a [Nat Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) and assign one of the Elastic IPs (\*The other 2 will be used by [Bastion hosts](https://aws.amazon.com/solutions/implementations/linux-bastion/))

![](./images/create-NAT.png)

- Edit a route in private route table, and associate it with the Nat Gateway.

  ![](./images/route-Nat.png)

### 9. Create a [Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html#CreatingSecurityGroups) for:

- **External [Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/):** External `ALB` will be available from the Internet

![](./images/ext-ALB-SG.png)

- **Bastion Servers:** Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type `curl www.canhazip.com`

![](./images/bastion-sg.png)

- **Nginx Servers:** Access to Nginx should only be allowed from an external Application Load balancer (ALB).

![](./images/nginx-sg.png)

- **Internal Application Load Balancer:** This is `not` an internet facing `ALB` rather used to distribute internal traffic comming from Nginx (reverse proxy) to our Webservers Auto Scalling Groups in our private subnets. It also helps us to prevent single point of failure.

![](./images/int-ALB-SG.png)

- **Webservers:** Access to Webservers should only be allowed from the internal ALB.

![](./images/webservers-sg.png)

- **Data Layer:** Access to the Data layer, which is comprised of [Amazon Relational Database Service (`RDS`)](https://aws.amazon.com/rds/) and [Amazon Elastic File System (EFS)](https://aws.amazon.com/efs/) must be carefully desinged - only webservers should be able to connect to `RDS`, while Nginx and Webservers will have access to `EFS Mountpoint`.

![](./images/data-sg.png)

All Security Groups

![](./images/SGs.png)

## TLS Certificates From Amazon Certificate Manager (ACM)

We will need TLS certificates to handle secured connectivity to our Application Load Balancers (ALB).

1. Navigate to AWS ACM
2. Request a public wildcard certificate for the domain name we registered in Cloudns
3. Use DNS to validate the domain name
4. Tag the resource

![](./images/ACM.png)

![](./images/cert-created.png)

- Ensure to create record on Route 53 after creating the certificate. This will generate a CNAME record type in Route 53. We will attach this certificate to all the load balancers.

  - Click, Create records in Route 53 > Create records

    ![](./images/create-record-cname.png)

    ![](./images/cname-created.png)

    Copy the CNAME name and the CNAME value generated then
    Go to Cloudns, Click on CNAME > Add new record. Paste the CNAME name and value to validate the record for AWS to issue the certificate (The Certificate status remain pending unitl the record is validated by the Domain provider.)

    ![](./images/validate-records.png)

    ![](./images/cloudns-records.png)

    After validating the record, AWS will issue the Certificate and the status changes to issued.

    ![](./images/issued-cert.png)

[def]: ./image/org.jpg
