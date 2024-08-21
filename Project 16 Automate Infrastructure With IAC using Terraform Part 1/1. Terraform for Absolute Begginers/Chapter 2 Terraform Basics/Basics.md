# Chapter 2: Terraform Basics

## Installing and Setting Up Terraform

Before we dive into the basics of Terraform, let's get started with installing and setting up Terraform on your system.

**Installing Terraform**

Terraform is available for Windows, macOS, and Linux platforms. You can download the latest version of Terraform from the official HashiCorp website: <https://www.terraform.io/downloads.html>

**Setting Up Terraform**

Once you've downloaded and installed Terraform, you can verify the installation by opening a terminal or command prompt and running the following command:

```bash
terraform --version
```

This should display the version of Terraform installed on your system.

## Terraform Configuration Syntax and Structure

Terraform configurations are written in a human-readable format using a syntax similar to JSON. The basic structure of a Terraform configuration file consists of:

- **Providers**: Define the cloud providers or services you want to interact with.
- **Resources**: Define the infrastructure resources you want to create or manage.
- **Data Sources**: Define data sources that can be used to retrieve information about existing infrastructure.
- **Variables**: Define input variables that can be used to customize the configuration.
- **Outputs**: Define output values that can be used to expose information about the created resources.

A typical Terraform configuration file has a `.tf` extension and is written in a declarative syntax.

## Resources, Providers, and Data Sources

**Resources**:

Resources are the core building blocks of Terraform configurations. They define the infrastructure resources you want to create or manage, such as virtual machines, databases, or storage buckets.

Here's an example of a simple resource definition:

```bash
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"
}
```

This resource definition creates an AWS EC2 instance with the specified AMI and instance type.

**Providers**:

Providers are responsible for interacting with the underlying cloud providers or services. Terraform supports a wide range of providers, including AWS, Azure, Google Cloud, and more.

Here's an example of a provider definition:

```bash
provider "aws" {
  region = "us-west-2"
}
```

This provider definition specifies the AWS provider and sets the region to `us-west-2`.

**Data Sources**:

Data sources are used to retrieve information about existing infrastructure resources. They can be used to populate input variables or to retrieve information about resources that are not managed by Terraform.

Here's an example of a data source definition:

```bash
data "aws_ami" "example" {
  most_recent = true
  owners      = ["self"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
}
```

This data source definition retrieves the most recent Ubuntu AMI in the `us-west-2` region.

## Variables, Outputs, and Modules\*\*

**Variables**:

Variables are used to customize the Terraform configuration by allowing you to input values that can be used throughout the configuration.

Here's an example of a variable definition:

```bash
variable "instance_type" {
  type = string
  default = "t2.micro"
}
```

This variable definition defines an input variable `instance_type` with a default value of `t2.micro`.

**Outputs**:

Outputs are used to expose information about the created resources. They can be used to output values that can be used by other Terraform configurations or by external tools.

Here's an example of an output definition:

```bash
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

This output definition exposes the public IP address of the created EC2 instance.

**Modules**:

Modules are reusable Terraform configurations that can be used to simplify complex infrastructure deployments. They can be used to group related resources and providers together.

Here's an example of a module definition:

```bash
module "ec2_instance" {
  source = path.module("ec2_instance", "main.tf")

  instance_type = var.instance_type
}
```

This module definition imports a module `ec2_instance` from a separate file and passes an input variable `instance_type` to the module.
