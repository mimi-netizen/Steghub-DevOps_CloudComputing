## WHEN TO USE WORKSPACES OR DIRECTORY?

To separate environments with significant configuration differences, use a directory structure. Use workspaces for environments that
do not greatly deviate from each other, to avoid duplication of your configurations. Try both methods in the sections below to help
you understand which will serve your infrastructure best.

For now, you can read more about both alternatives
[here](https://developer.hashicorp.com/terraform/tutorials/modules/organize-configuration) and try both methods yourself, but we will
explore them better in **next projects**
**Create Environment-Specific Variable Files**
In our root directory create separate `.tfvars` files for each environment to specify the variable values. For example:

- `dev.tfvars`
- `1sit.tfvars`
- `uat.tfvars`
- `preprod.tfvars`
- `prod.tfvars`
  **Then Create workspaces for each environment**

```js
terraform workspace new dev
terraform workspace new sit
terraform workspace new uat
terraform workspace new preprod
terraform workspace new prod
```

**Create workspaces for each environment**

```js
terraform workspace new dev
terraform workspace new sit
terraform workspace new uat
terraform workspace new preprod
terraform workspace new prod
```

**Switch Workspace and Apply Configuration**
When you want to apply configurations to a specific environment, switch to the corresponding workspace and use the appropriate variable file.
For example, to deploy to the dev environment:

```js
terraform workspace select dev
terraform plan -var-file=dev.tfvars
terraform apply -var-file=dev.tfvars
```

> **It is enough as an introduction; we will explore them better in the next projects.**

**Security Groups refactoring with dynamic block**

For repetitive blocks of code you can use [dynamic blocks](https://developer.hashicorp.com/terraform/language/expressions/dynamic-blocks)
in Terraform, to get to know more how to use them – [watch this video](https://youtu.be/tL58Qt-RGHY).

**Refactor Security Groups create all security groups dynamically**
![image](image)

**creating dynamic ingress security groups**
![image](image)

**EC2 refactoring with Map and Lookup**
Remember, every piece of work you do, always try to make it dynamic to accommodate future changes. Amazon Machine Image (AMI) is a
regional service which means it is only available in the region it was created. But what if we change the region later, and want to
dynamically pick up AMI IDs based on the available AMIs in that region? This is where we will introduce Map and Lookup functions.

**Map uses a key and value pairs as a data structure that can be set as a default type for variables.**

```bash
variable "images" {
    type = "map"
    default = {
        us-east-1 = "image-1234"
        us-west-2 = "image-23834"
    }
}
```

![image](image)

To select an appropriate AMI per region, we will use a lookup function which has following syntax: lookup(map, key, [default]).

Note: A default value is better to be used to avoid failure whenever the map data has no key.

```bash
resource "aws_instace" "web" {
    ami  = "${lookup(var.images, var.region), "ami-12323"}
   }
```

![image](image)

Now, the lookup function will load the variable images using the first parameter. But it also needs to know which of the key-value
pairs to use. That is where the second parameter comes in. The key us-east-1 could be specified, but then we will not be doing
anything dynamic there, but if we specify the variable for region, it simply resolves to one of the keys. That is why we have used
var.region in the second parameter.

**Conditional Expressions**
If you want to make some decision and choose some resource based on a condition – you shall use
[Terraform Conditional Expressions](https://developer.hashicorp.com/terraform/language/expressions/conditionals).

In general, the syntax is as following: `condition` ? `true_val` : `false_val`

Read following snippet of code and try to understand what it means:

```bash
resource "aws_db_instance" "read_replica" {
  count               = var.create_read_replica == true ? 1 : 0
  replicate_source_db = aws_db_instance.this.id
}
```

- `true` # condition `if true`
- ? # means, set to `1`
- : # means, otherwise, set to `0`

**Terraform Modules and best practices to structure your .tf codes**

By this time, you might have realized how difficult is to navigate through all the Terraform blocks if they are all written in a
single long `.tf` file. As a **DevOps engineer**, you must produce reusable and comprehensive `IaC` code structure, and one of the tool that Terraform provides out of the box is **Modules**.

**Modules** serve as _containers_ that allow to logically group Terraform codes for similar resources in the same domain
(e.g. Compute, Networking, AMI, etc.). One root module can call other child modules and insert their configurations when applying
Terraform config. This concept makes your code structure neater, and it allows different team members to work on different parts of
configuration at the same time. You can also create and publish your modules to Terraform Registry for others to use and use someone’s modules in your projects.

Module is just a collection of `.tf` and/or `.tf.json` files in a directory.

You can refer to existing child modules from your root module by specifying them as a source, like this:

```bash
module "network" {
  source = "./modules/network"
}
```

![image](image)

Note that the path to `network` module is set as relative to your working directory.

Or you can also directly access resource outputs from the modules, like this:

```bash
resource "aws_elb" "example" {
  # ...
  instances = module.servers.instance_ids
}
```

In the example above, you will have to have module ‘servers’ to have output file to expose variables for this resource.
