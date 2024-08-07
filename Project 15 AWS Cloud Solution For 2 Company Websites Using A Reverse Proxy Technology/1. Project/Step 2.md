# Setup EFS

[Amazon Elastic File System (Amazon EFS)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html) provides a simple, scalable, fully managed elastic [Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System) for use with AWS Cloud services and on-premises resources. In this project, we will utulize EFS service and mount filesystems on both Nginx and Webservers to store data.

1. Create an EFS filesystem

2. Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer.
   **NB:** Any subnet we specify our mount target, the Amazon EFS becomes available in that subnet. As such, we will specify it in private webserver subnets so that all the resources in that subnet will have the ability to mount to the file system.

3. Associate the Security groups created earlier for data layer.

![](./images/create-FS.png)
![](./images/create-FS-cont.png)

![](./images/efs.png)

4. Create an EFS access point.

This will specify where the webservers will mount with, thus creating 2 mount points for Tooling and Wordpress servers each.

Access point for wordpress server

![](./images/wp-access-point.png)

Access point for tooling server

![](./images/tooling-access-pt.png)

EFS access points

![](./images/efs-access-points.png)
![](./images/EFS-access-pt.png)

# Setup RDS

### Pre-requisite:

Create a `KMS` key from Key Management Service (KMS) to be used to encrypt the database instance.

![](./images/kms.png)
![](./images/kms-label.png)
![](./images/kms-admin-permit.png)
![](./images/kms-usage-permit.png)
![](./images/kms-created.png)

Amazon Relational Database Service (Amazon RDS) is a managed distributed relational database service by Amazon Web Services. This web service running in the cloud designed to simplify setup, operations, maintenans & scaling of relational databases. Without RDS, Database Administrators (DBA) have more work to do, due to RDS, some DBAs have become jobless.
To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a [multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html) set up of [RDS MySQL database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html) instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones. We will not consider possible failure of the whole Region, but for this AWS also has a solution - this is a more advanced concept that will be discussed in following projects.

To configure `RDS`, follow steps below:

1. Create a `subnet group` and add 2 private subnets (data Layer)

![](./images/rds-subnet-grp.png)
![](./images/rds-subnet-grp-created.png)

2. Create an RDS Instance for `mysql 8.*.*`

![](./images/mysql.png)

3. To satisfy our architectural diagram, you will need to select either `Dev/Test` or `Production` Sample Template. But to minimize AWS cost, you can select the `Do not create a standby instance` option under `Availability & durability` sample template (The production template will enable Multi-AZ deployment)

![](./images/rds-template.png)

4. Configure other settings accordingly (For test purposes, most of the default settings are good to go). In the real world, you will need to size the database appropriately. You will need to get some information about the usage. If it is a highly transactional database that grows at 10GB weekly, you must bear that in mind while configuring the initial storage allocation, storage autoscaling, and maximum storage threshold.

![](./images/rds-settings-cont.png)

5. Configure VPC and security (ensure the database is not available from the Internet)

![](./images/rds-vpc.png)
![](./images/rds-vpc-cont.png)

6. Configure backups and retention
7. Encrypt the database using the KMS key created earlier
8. Enable [CloudWatch](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/monitoring-cloudwatch.html) monitoring and export Error and Slow Query logs (for production, also include Audit)

![](./images/rds-backup.png)

![](./images/rds.png)

# Proceed With Compute Resources

We will need to set up and configure compute resources inside our VPC. The recources related to compute are:

- [EC2 Instances](https://www.amazonaws.cn/en/ec2/instance-types/)
- [Launch Templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html)
- [Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
- [Autoscaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html)
- [TLS Certificates](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Application Load Balancers (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

## Set Up Compute Resources for Nginx, Bastion and Webservers

NB:
To create the `Autoscaling Groups`, we need `Launch Templates` and `Load Balancers`.
The Launch Templates requires `AMI` and `Userdata` while the Load balancer requires `Target Group`

### Provision EC2 Instances for Nginx, Bastion and Webservers

1. Create EC2 Instances based on CentOS [Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) per each [Availability Zone in the same Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). Use EC2 instance of [T2 family](https://aws.amazon.com/ec2/instance-types/t2/) (e.g. t2.micro or similar) with security group (All traffic - anywhere).

![](./images/base-servers.png)

2. Ensure that it has the following software installed:

- python
- ntp
- net-tools
- vim
- wget
- telnet
- epel-release
- htop

### For Nginx, Bastion and Webserver.

**For Nginx**

```bash
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```

![](./images/epel-release.png)

```bash
sudo yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm
```

![](./images/dnf-utils.png)

```bash
sudo yum install wget vim python3 telnet htop net-tools ntp -y
```

![](./images/err-ntp.png)

The error occured because the ntp package is not available in the repositories for our Enterprise Linux 9 system. Instead of ntp, chrony can be used, which is the default NTP implementation in newer versions of RHEL and its derivatives, including Enterprise Linux 9.

```bash
sudo yum install wget vim python3 telnet htop git mysql net-tools chrony -y
```

![](./images/install-software-nginx.png)

```bash
sudo systemctl start chronyd
sudo systemctl enable chronyd
sudo systemctl status chronyd
```

![](./images/start-chrony.png)

**NB**: **Repeat the above steps for Bastion and Webservers**

### Nginx and Webserver (Set SELinux policies so that our servers can function properly on all the redhat instance).

**For Nginx**

```bash
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db=1
sudo setsebool -P httpd_execmem=1
sudo setsebool -P httpd_use_nfs 1
```

![](./images/selinux-policy.png)

**NB: Repeat the step above for Webservers**

### Install Amazon EFS utils for mounting the targets on the Elastic file system (Nginx and Webserver).

**For Nginx**

```bash
git clone https://github.com/aws/efs-utils

cd efs-utils
```

![](./images/clone-efs-utils.png)

```bash
sudo yum install -y make
```

![](./images/install-make.png)

```bash
sudo yum install -y rpm-build
```

![](./images/install-rpm-build.png)

```bash
# openssl-devel is needed by amazon-efs-utils-2.0.4-1.el9.x86_64
sudo yum install openssl-devel -y
```

![](./images/install-openssl-devel.png)

```bash
# Cargo command needs to be installed as it is necessary for building the Rust project included in the source.
sudo yum install cargo -y
```

![](./images/install-cargo.png)

```bash
sudo make rpm
```

![](./images/make-rpm.png)

```bash
sudo yum install -y  ./build/amazon-efs-utils*rpm
```

![](./images/install-efs-utils.png)

**Repeat the steps above for Webservers**

We are using an ACM (Amazon Certificate Manager) certificate for both our external and internal load balancers.
But for some reasons, we might want to add a self-signed certificate:

- **Compliance Requirements:**

  - Certain industry regulations and standards (e.g., PCI DSS, HIPAA) require end-to-end encryption, including between internal load balancers and backend servers (within a private network).

- **Defense in Depth:**
  - Adding another layer of security by encrypting traffic between internal load balancers and web servers can provide additional protection.

When generating the certificate, In the common name, enter the private IPv4 dns of the instance (for Webserver and Nginx). We use the certificate by specifying the path to the file fnc.crt and fnc.key in the nginx configuration.

## Set up self-signed certificate for the nginx instance

```bash
sudo mkdir /etc/ssl/private

sudo chmod 700 /etc/ssl/private

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/fnc.key -out /etc/ssl/certs/fnc.crt
```

![](./images/nginx-ssl-key.png)

```bash
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

![](./images/nginx-ssl-cert.png)

## Set up self-signed certificate for the Apache Webserver instance

```bash
sudo yum install -y mod_ssl
```

![](./images/install-mod-ssl.png)

```bash
sudo openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/fnc.key -x509 -days 365 -out /etc/pki/tls/certs/fnc.crt
```

![](./images/apache-tls-key.png)

```bash
# Edit the ssl.conf to conform with the key and crt file created.
sudo vim /etc/httpd/conf.d/ssl.conf
```

![](./images/edit-apache-config.png)

## [Create an AMI](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/tkv-create-ami-from-instance.html) out of the EC2 instances

On the EC2 instance page, Go to Actions > Image and templates > Create image

**For Bastion AMI**

![](./images/bastion-ami.png)

**For Nginx AMI**

![](./images/nginx-ami.png)

**For Webservers AMI**

![](./images/webservers-ami.png)

**All AMIs**

![](./images/all-ami.png)

## CONFIGURE TARGET GROUPS

Create Target groups for Nginx, Worpress and Tooling

**For Nginx Target Group**

![](./images/nginx-tg.png)
![](./images/nginx-tg-cont.png)

**For Wordpress Target Group**

![](./images/wordpress-tg.png)
![](./images/wordpress-tg-cont.png)

**For Tooling Target Group**

![](./images/tooling-tg.png)
![](./images/tooling-tg-cont.png)