# Chapter 3: Terraform Workflow\*\*

In this chapter, we'll cover the Terraform workflow, including writing Terraform configurations, running Terraform commands, and state management.

## Writing Terraform Configurations

A Terraform configuration is a human-readable file that defines the infrastructure you want to create or manage. Terraform configurations are written in a declarative syntax, which means you define what you want to create, rather than how to create it.

Here's an example of a simple Terraform configuration file:

```bash
# Configure the AWS provider
provider "aws" {
  region = "us-west-2"
}

# Create an EC2 instance
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"
}
```

This configuration file defines an AWS provider and creates an EC2 instance with a specific AMI and instance type.

## Running Terraform Commands

Terraform provides several commands that you can use to manage your infrastructure. Here are the most commonly used Terraform commands:

**`terraform init`**

The `terraform init` command initializes a Terraform working directory. This command creates a `.terraform` directory and downloads the required provider plugins.

**`terraform plan`**

The `terraform plan` command generates an execution plan, which shows you what changes Terraform will make to your infrastructure. This command is useful for reviewing the changes before applying them.

**`terraform apply`**

The `terraform apply` command applies the changes outlined in the execution plan. This command creates or updates your infrastructure according to the Terraform configuration file.

**`terraform destroy`**

The `terraform destroy` command destroys the infrastructure defined in the Terraform configuration file. This command is useful for cleaning up resources that are no longer needed.

Here's an example of running Terraform commands:

```bash
$ terraform init
$ terraform plan
$ terraform apply
$ terraform destroy
```

## State Management and the Terraform State File

Terraform uses a state file to keep track of the infrastructure it manages. The state file is a JSON file that stores information about the resources created or updated by Terraform.

The Terraform state file is created when you run `terraform init` for the first time. The state file is updated every time you run `terraform apply` or `terraform destroy`.

Here's an example of a Terraform state file:

```bash
{
  "version": 4,
  "terraform_version": "0.14.0",
  "serial": 1,
  "lineage": "1234567890",
  "outputs": {
    "instance_ip": {
      "value": "123.456.789.0",
      "type": "string"
    }
  },
  "resources": [
    {
      "address": "aws_instance.example",
      "mode": "managed",
      "type": "aws_instance",
      "name": "example",
      "instances": [
        {
          "attributes": {
            "ami": "ami-abc123",
            "instance_type": "t2.micro",
            "public_ip": "123.456.789.0"
          }
        }
      ]
    }
  ]
}
```

This state file shows the infrastructure resources created by Terraform, including an EC2 instance with a specific AMI and instance type.
