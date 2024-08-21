# Chapter 7: Terraform Modules

Terraform modules are a way to organize and reuse Terraform configurations. They allow you to break down a large infrastructure into smaller, more manageable pieces, making it easier to maintain and update your infrastructure.

## Creating and Using Modules

A Terraform module is a collection of Terraform files that define a specific infrastructure component, such as a virtual network, a database, or a web server. To create a module, you need to create a new directory with a `main.tf` file and any other necessary Terraform files.

Here's an example of a simple module that creates an EC2 instance:

```bash
# File: modules/ec2_instance/main.tf
resource "aws_instance" "this" {
  instance_type = var.instance_type
  ami           = var.ami
}
```

To use a module, you need to create a `module` block in your main Terraform configuration file, specifying the module's source and any input variables:

```bash
# File: main.tf
module "ec2_instance" {
  source = file("./modules/ec2_instance")

  instance_type = "t2.micro"
  ami           = "ami-abc123"
}
```

### Creating a Custom Module in Terraform

Creating a custom module in Terraform is a great way to organize and reuse your infrastructure code. Here's a step-by-step guide to create a custom module:

**1. Create a new directory for your module**

Create a new directory for your module, and navigate into it:

```bash
mkdir my_module
cd my_module
```

**2. Create a `main.tf` file**

Create a `main.tf` file in the directory, which will contain the module's Terraform configuration:

```bash
# File: main.tf
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"
}
```

In this example, the module creates an AWS EC2 instance with a specific AMI and instance type.

**3. Add input variables (optional)**

If you want to make your module more flexible, you can add input variables that can be customized by users. Create a `variables.tf` file:

```bash
# File: variables.tf
variable "instance_type" {
  type        = string
  default     = "t2.micro"
  description = "The instance type for the EC2 instance"
}

variable "ami" {
  type        = string
  default     = "ami-abc123"
  description = "The AMI for the EC2 instance"
}
```

In this example, the module has two input variables: `instance_type` and `ami`. Users can customize these variables when using the module.

**4. Add output values (optional)**

If you want to expose values from your module to users, you can add output values. Create an `outputs.tf` file:

```bash
# File: outputs.tf
output "instance_id" {
  value = aws_instance.example.id
  description = "The ID of the EC2 instance"
}
```

In this example, the module outputs the ID of the EC2 instance.

**5. Create a `README.md` file (optional)**

Create a `README.md` file to provide documentation for your module:

```markdown
# File: README.md

# My Module

This module creates an AWS EC2 instance with a specific AMI and instance type.

## Inputs

- `instance_type`: The instance type for the EC2 instance (default: `t2.micro`)
- `ami`: The AMI for the EC2 instance (default: `ami-abc123`)

## Outputs

- `instance_id`: The ID of the EC2 instance
```

**6. Publish your module (optional)**

If you want to share your module with others, you can publish it to the Terraform Registry or a private registry.

**Using your custom module**

To use your custom module, create a new Terraform configuration file that imports the module:

```bash
# File: main.tf
module "my_module" {
  source = file("./my_module")

  instance_type = "t2.small"
  ami           = "ami-def456"
}
```

In this example, the Terraform configuration imports the `my_module` module and customizes its input variables.

## Module Input and Output Variables

Modules can have input variables, which are used to customize the module's behavior, and output variables, which are used to expose the module's resources to other parts of the Terraform configuration.

In the example above, `instance_type` and `ami` are input variables that are passed to the module. The module can then use these variables to create the EC2 instance.

Output variables are used to expose the resources created by the module. For example:

```bash
# File: modules/ec2_instance/main.tf
resource "aws_instance" "this" {
  instance_type = var.instance_type
  ami           = var.ami
}

output "instance_id" {
  value = aws_instance.this.id
}
```

In this example, the `instance_id` output variable is used to expose the ID of the EC2 instance created by the module.

## Module Versioning and the Terraform Registry

Terraform modules can be versioned, which allows you to track changes and dependencies between different versions of the module. The Terraform Registry is a central repository of Terraform modules that can be easily installed and used in your Terraform configurations.

To publish a module to the Terraform Registry, you need to create a `module` block with a `version` attribute:

```bash
# File: modules/ec2_instance/main.tf
module "ec2_instance" {
  version = "1.0.0"

  resource "aws_instance" "this" {
    instance_type = var.instance_type
    ami           = var.ami
  }
}
```

You can then publish the module to the Terraform Registry using the `terraform module publish` command.

To use a module from the Terraform Registry, you need to specify the module's name and version in the `module` block:

```bash
# File: main.tf
module "ec2_instance" {
  source = "registry.terraform.io/terraform-aws-modules/ec2-instance/aws"
  version = "1.0.0"

  instance_type = "t2.micro"
  ami           = "ami-abc123"
}
```

In this example, the module is downloaded from the Terraform Registry and used in the Terraform configuration.

## How to test Terraform modules before deployment

Testing Terraform modules before deployment is an essential step to ensure that your infrastructure code is correct and works as expected. Here are some ways to test Terraform modules:

**1. Terraform Validation**

Terraform provides a built-in validation mechanism to check the syntax and structure of your Terraform configuration files. You can run `terraform validate` to check for any errors or warnings in your module.

**2. Terraform Formatting**

Terraform also provides a built-in formatting mechanism to ensure that your Terraform configuration files are formatted correctly. You can run `terraform fmt` to format your module's files.

**3. Terraform Init**

Before testing your module, you need to initialize the Terraform working directory by running `terraform init`. This command sets up the Terraform working directory and downloads the required providers.

**4. Terraform Plan**

The `terraform plan` command is used to generate an execution plan for your module. This command shows you what changes Terraform will make to your infrastructure without actually applying them.

**5. Terraform Apply with -destroy**

To test your module, you can run `terraform apply` with the `-destroy` flag. This flag tells Terraform to create the resources and then immediately destroy them. This way, you can test your module without actually deploying it to your infrastructure.

**6. Mocking Dependencies**

If your module depends on external resources or services, you can use mocking libraries like `terraform-mock` or `mockk` to simulate these dependencies. This allows you to test your module in isolation without relying on external resources.

**7. Integration Testing**

Integration testing involves testing your module with other Terraform configurations or infrastructure components. You can use tools like `terraform-test` or `kitchen-terraform` to automate integration testing for your module.

**8. CI/CD Pipelines**

Finally, you can integrate your module testing into a CI/CD pipeline using tools like Jenkins, CircleCI, or GitHub Actions. This allows you to automate testing for your module on every commit or push.

Here's an example of how you can test a Terraform module using `terraform plan` and `terraform apply` with `-destroy`:

```bash
# Initialize the Terraform working directory
terraform init

# Generate an execution plan
terraform plan -out=tfplan

# Apply the plan with -destroy to test the module
terraform apply -destroy -input=false tfplan

# Clean up the Terraform working directory
terraform destroy
```

By following these steps, you can ensure that your Terraform module is tested and validated before deployment.
