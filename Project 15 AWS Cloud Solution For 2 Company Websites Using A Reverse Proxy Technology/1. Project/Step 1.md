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

![vpc](image/vpc.jpg)

### Enable DNS hostnames.

Actions > Edit VPC Settings > Enable DNS hostnames

![](image/vpc1.jpg)

### 2. Create subnets as shown in the architecture

Create public and private subnets in each availablity zones respectively. Thus, we create public subnet in availability zone A and B respectively. We create 4 private subnets with respect to the diagram we are working with as such we create 2 private subnets each in availability zone A and B.

On the left panel menu of the VPC UI, click on Subnet > Create Subnet.

- VPC: 10.0.0.0/16

- Public Subnet 1: 10.0.1.0/24 in Zone A

- Public Subnet 2: 10.0.2.0/24 in Zone B

- Private Subnet 1: 10.0.3.0/24 in Zone A

- Private Subnet 2: 10.0.4.0/24 in Zone B

- Private Subnet 3: 10.0.5.0/24 in Zone A

- Private Subnet 4: 10.0.6.0/24 in Zone B

![](image/s1.jpg)
![](image/S2.jpg)
![](image/S3.jpg)
![](image/S4.jpg)
![](image/S5.jpg)
![](image/S6.jpg)
![](image/S7.jpg)
![](image/S8.jpg)

Enable Auto-assign public IPv4 address in the public subnets.
Actions > Edit subnet settings > Enable auto-assign public IPv4

![](image/s9.jpg)

### 3. Create a route table and associate it with public subnets

Create a public route table for the public subnets

**In the left-hand navigation pane, click on Route Tables > Create route table button**

Enter the following details:

- **Name tag:** Provide a name for your route table Public Route Table
- **VPC:** Select the VPC you created earlier.
- Click **Create route table.**

![](image/route.jpg)

Associate it with public subnets.
Click on the route table > Subnet Association > Edit Subnet Association.

- Select the Public Route Table from the list.
- Click on the Subnet associations tab.
- Click Edit subnet associations.
- In the Select subnets section, check the boxes for Public Subnet 1 and Public Subnet 2.
- Click Save associations.

![](image/sub.jpg)

The public route table and it's associated public subnets

![](image/subnet.jpg)

### 4. Create a route table and associate it with private subnets

Create a private route table for the private subnets and Associate it with private subnets

![](image/private.jpg)

### 5. Create an [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)

Click on Internet Gateways on the left-hand side > Create internet gateway > Enter a name for your internet gateway > Create internet gateway

![](image/ig.jpg)

Attach it to the created VPC: Select the internet gateway you just created > Click the Actions dropdown, then select Attach to VPC >Select the VPC you created earlier >Click Attach internet gateway.

![](image/at.jpg)
![](image/at1.jpg)

### 6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)

Go back to Route Tables and select the Public Route Table > Click on the Routes tab > Click Edit routes. Click Add route > Destination: 0.0.0.0/0 > Target: Select the internet gateway you created > Click Save routes

![](image/ig1.jpg)

### 7. Create 3 [Elastic IPs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

one for `Nat getway` and the other 2 will be used by `Bastion hosts`

Create Elastic IP to configured with the NAT gateway. The NAT gateway enables connection from the public subnet to private subnet and it needs a static ip to make this happen.

- VPC > Elastic IP addresses > Allocate Elastic IP address - add a name tag and click on allocate

![](image/eip.jpg)

### 8. Create a [Nat Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) and assign one of the Elastic IPs (\*The other 2 will be used by [Bastion hosts](https://aws.amazon.com/solutions/implementations/linux-bastion/))

Create a Nat Gateway and assign the Elastic IPs Click on VPC > NAT gateways > Create NAT gateway

Select a Public Subnet Connection Type: Public Allocate Elastic IP

![](image/nat.jpg)

- Edit a route in private route table, and associate it with the Nat Gateway.

  ![](image/nat1.jpg)

### 9. Create a [Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html#CreatingSecurityGroups) for:

- **External [Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/):** External `ALB` will be available from the Internet

![](image/alb.jpg)

- **Bastion Servers:** Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type `curl www.canhazip.com`

![](image/bas.jpg)

- **Nginx Servers:** Access to Nginx should only be allowed from an external Application Load balancer (ALB).

![](image/nginx.jpg)

- **Internal Application Load Balancer:** This is `not` an internet facing `ALB` rather used to distribute internal traffic comming from Nginx (reverse proxy) to our Webservers Auto Scalling Groups in our private subnets. It also helps us to prevent single point of failure.

![](image/int.jpg)

- **Webservers:** Access to Webservers should only be allowed from the internal ALB.

![](image/WEB.jpg)

- **Data Layer:** Access to the Data layer, which is comprised of [Amazon Relational Database Service (`RDS`)](https://aws.amazon.com/rds/) and [Amazon Elastic File System (EFS)](https://aws.amazon.com/efs/) must be carefully desinged - only webservers should be able to connect to `RDS`, while Nginx and Webservers will have access to `EFS Mountpoint`.

![](image/ws.jpg)

All Security Groups

![](image/sg.jpg)

## TLS Certificates From Amazon Certificate Manager (ACM)

We will need TLS certificates to handle secured connectivity to our Application Load Balancers (ALB).

1. Navigate to AWS ACM
2. Request a public wildcard certificate for the domain name we registered in Cloudns
3. Use DNS to validate the domain name
4. Tag the resource

- Ensure to create record on Route 53 after creating the certificate. This will generate a CNAME record type in Route 53. We will attach this certificate to all the load balancers.

  - Click, Create records in Route 53 > Create records

    ![](image/rec.jpg)

    Copy the CNAME name and the CNAME value generated then
    Go to Cloudns, Click on CNAME > Add new record. Paste the CNAME name and value to validate the record for AWS to issue the certificate (The Certificate status remain pending unitl the record is validated by the Domain provider.)

    ![](image/cname.jpg)

    ![](image/cn.jpg)

    After validating the record, AWS will issue the Certificate and the status changes to issued.

    ![](./images/issued-cert.png)

[def]: ./image/org.jpg
