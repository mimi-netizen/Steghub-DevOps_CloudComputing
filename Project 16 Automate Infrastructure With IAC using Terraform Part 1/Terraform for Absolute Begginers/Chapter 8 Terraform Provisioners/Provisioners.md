# Chapter 8: Terraform Provisioners

Terraform provisioners are used to execute scripts or commands on resources after they have been created. This allows you to perform post-creation tasks, such as installing software, configuring systems, or running scripts. Provisioners are a powerful tool in Terraform that can help you automate the deployment of your infrastructure.

## Using Provisioners for Post-Creation Tasks

Provisioners are used to execute scripts or commands on resources after they have been created. You can use provisioners to perform a wide range of tasks, such as:

- Installing software or packages
- Configuring systems or services
- Running scripts or commands
- Creating files or directories
- Setting environment variables

Here's an example of how you can use a provisioner to install Apache on a newly created EC2 instance:

```bash
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("~/.ssh/my_private_key")
    }

    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y apache2",
      "sudo systemctl start apache2",
      "sudo systemctl enable apache2"
    ]
  }
}
```

In this example, the provisioner uses the `remote-exec` type to connect to the EC2 instance via SSH and runs a series of commands to install and configure Apache.

## Local-Exec and Remote-Exec Provisioners

Terraform provides two types of provisioners: `local-exec` and `remote-exec`.

- `local-exec` provisioners execute commands on the local machine running Terraform. This can be useful for tasks such as creating files or directories, or running scripts that don't require access to the remote resource.

- `remote-exec` provisioners execute commands on the remote resource, such as an EC2 instance or a container. This can be useful for tasks such as installing software, configuring systems, or running scripts that require access to the remote resource.

Here's an example of how you can use a `local-exec` provisioner to create a file on the local machine:

```bash
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo 'Hello World!' > hello.txt"
  }
}
```

In this example, the provisioner uses the `local-exec` type to execute a command on the local machine, creating a file called `hello.txt` with the contents "Hello World!".

## File Provisioner and Connection Settings

The `file` provisioner is used to upload files to a remote resource. This can be useful for tasks such as uploading configuration files, scripts, or binaries to a remote resource.

Here's an example of how you can use a `file` provisioner to upload a file to an EC2 instance:

```bash
resource "aws_instance" "example" {
  ami           = "ami-abc123"
  instance_type = "t2.micro"

  provisioner "file" {
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("~/.ssh/my_private_key")
    }

    source      = "path/to/local/file"
    destination = "/remote/path/to/file"
  }
}
```

In this example, the provisioner uses the `file` type to upload a file from the local machine to the EC2 instance.

Connection settings, such as the `connection` block, are used to specify the details of the connection to the remote resource. This can include the type of connection, the host, user, and private key.

## Provisioners vs Modules

Provisioners and modules are two distinct concepts in Terraform, each serving a different purpose in infrastructure deployment.

**Provisioners**

Provisioners are used to execute scripts or commands on resources after they have been created. They are used to perform post-creation tasks, such as:

- Installing software or packages
- Configuring systems or services
- Running scripts or commands
- Creating files or directories
- Setting environment variables

Provisioners are typically used to perform tasks that require access to the resource, such as installing software or configuring systems. They are executed after the resource has been created, and can be used to customize the resource to meet specific requirements.

**Modules**

Modules, on the other hand, are reusable collections of Terraform configurations that define a specific infrastructure component, such as a virtual network, a database, or a web server. Modules are used to organize and reuse Terraform code, making it easier to manage complex infrastructure deployments.

Modules can include:

- Resources: Such as EC2 instances, S3 buckets, or RDS databases
- Data sources: Such as AWS IAM roles or AWS regions
- Provisioners: Such as scripts or commands to install software or configure systems
- Output values: Such as IP addresses or DNS names

Modules are used to define a self-contained infrastructure component that can be reused across multiple deployments. They are typically used to define a specific infrastructure component, such as a web server or a database, that can be deployed multiple times with different configurations.

**Key differences**

Here are the key differences between provisioners and modules:

- **Purpose**: Provisioners are used to execute scripts or commands on resources after creation, while modules are used to define reusable infrastructure components.
- **Scope**: Provisioners are used to customize individual resources, while modules are used to define a collection of resources that work together to form an infrastructure component.
- **Reusability**: Provisioners are not reusable, while modules are designed to be reusable across multiple deployments.
- **Composition**: Provisioners are used to perform tasks on individual resources, while modules are composed of multiple resources and provisioners that work together to form an infrastructure component.
