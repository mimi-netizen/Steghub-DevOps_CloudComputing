# FIXING THE PROBLEMS BY CODE REFACTORING

- Fixing Hard Coded Values: We will introduce variables, and remove hard coding.
- Starting with the provider block, declare a variable named region, give it a default value, and update the provider section by referring to the declared variable.

```bash
variable "region" {
        default = "us-east-1"
    }

    provider "aws" {
        region = var.region
    }
```

- Do the same to cidr value in the vpc block, and all the other arguments.

```bash
variable "region" {
        default = "us-east-1"
    }

    variable "vpc_cidr" {
        default = "172.16.0.0/16"
    }

    variable "enable_dns_support" {
        default = "true"
    }

    variable "enable_dns_hostnames" {
        default ="true"
    }

    variable "enable_classiclink" {
        default = "false"
    }

    variable "enable_classiclink_dns_support" {
        default = "false"
    }

    provider "aws" {
    region = var.region
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = var.vpc_cidr
    enable_dns_support             = var.enable_dns_support
    enable_dns_hostnames           = var.enable_dns_support
    enable_classiclink             = var.enable_classiclink
    enable_classiclink_dns_support = var.enable_classiclink

    }
```

- **Fixing multiple resource blocks**: This is where things become a little tricky. It’s not complex, we are just going to introduce
  some interesting concepts. Loops & Data sources

Terraform has a functionality that allows us to pull data which exposes information to us. For example, every region has Availability
Zones (AZ). Different regions have from 2 to 4 Availability Zones. With over 20 geographic regions and over 70 AZs served by AWS,
it is impossible to keep up with the latest information by hard coding the names of AZs. Hence, we will explore the use of Terraform’s
Data Sources to fetch information outside of Terraform. In this case, from AWS

Let us fetch Availability zones from AWS, and replace the hard coded value in the subnet’s availability_zone section.

```bash
        # Get list of availability zones
        data "aws_availability_zones" "available" {
        state = "available"
        }
```

To make use of this new data resource, we will need to introduce a `count` argument in the subnet block: Something like this.

```bash
 # Create public subnet1
    resource "aws_subnet" "public" {
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```

Let us quickly understand what is going on here.

- The count tells us that we need 2 subnets. Therefore, Terraform will invoke a loop to create 2 subnets.
- The data resource will return a list object that contains a list of AZs. Internally, Terraform will receive the data like this

```bash
["us-east-1a", "us-east-1b"]
```

Each of them is an index, the first one is index 0, while the other is index 1. If the data returned had more than 2 records, then
the index numbers would continue to increment.

Therefore, each time Terraform goes into a loop to create a subnet, it must be created in the retrieved AZ from the list. Each loop
will need the index number to determine what AZ the subnet will be created. That is why we have
data.aws_availability_zones.available.names[count.index] as the value for availability_zone. When the first loop runs, the first
index will be 0, therefore the AZ will be eu-central-1a. The pattern will repeat for the second loop.

But we still have a problem. If we run Terraform with this configuration, it may succeed for the first time, but by the time it goes
into the second loop, it will fail because we still have cidr_block hard coded. The same cidr_block cannot be created twice within
the same VPC. So, we have a little more work to do.

## Let’s make cidr*block \_dynamic*.

We will introduce a function cidrsubnet() to make this happen. It accepts 3 parameters. Let us use it first by updating the
configuration, then we will explore its internals.

```bash
# Create public subnet1
    resource "aws_subnet" "public" {
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```

A closer look at cidrsubnet – this function works like an algorithm to dynamically create a subnet CIDR per AZ. Regardless of the
number of subnets created, it takes care of the cidr value per subnet.

Its parameters are cidrsubnet(prefix, newbits, netnum)

- The prefix parameter must be given in CIDR notation, same as for VPC.
- The newbits parameter is the number of additional bits with which to extend the prefix. For example, if given a prefix ending with
  /16 and a newbits value of 4, the resulting subnet address will have length /20
- The netnum parameter is a whole number that can be represented as a binary integer with no more than newbits binary digits, which
  will be used to populate the additional bits added to the prefix

You can experiment how this works by entering the terraform console and keep changing the figures to see the output.

- On the terminal, run terraform console
- type cidrsubnet("172.16.0.0/16", 4, 0)
- Hit enter
- See the output
- Keep change the numbers and see what happens.
- To get out of the console, type exit

## The final problem to solve is removing hard coded count value.

If we cannot hard code a value we want, then we will need a way to dynamically provide the value based on some input. Since the data
resource returns all the AZs within a region, it makes sense to count the number of AZs returned and pass that number to the count
argument.

To do this, we can introuduce length() function, which basically determines the length of a given list, map, or string.

Since data.aws_availability_zones.available.names returns a list like ["us-east-1a", "us-east-1b", "us-east-1c"] we can pass
it into a lenght function and get number of the AZs.

length(["us-east-1a", "us-east-1b", "us-east-1c"])

Open up terraform console and try it

Now we can simply update the public subnet block like this

```bash
# Create public subnet1
    resource "aws_subnet" "public" {
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```

**Observations:**

- What we have now, is sufficient to create the subnet resource required. But if you observe, it is not satisfying our business
  requirement of just 2 subnets. The length function will return number 3 to the count argument, but what we actually need is 2.

Now, let us fix this.

- Declare a variable to store the desired number of public subnets, and set the default value

```bash
variable "preferred_number_of_public_subnets" {
  default = 2
}
```

- Next, update the count argument with a condition. Terraform needs to check first if there is a desired number of subnets. Otherwise,
  use the data returned by the lenght function. See how that is presented below.

```bash
# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}
```

Now lets break it down:

- The first part var.preferred_number_of_public_subnets == null checks if the value of the variable is set to null or has some value
  defined.
- The second part ? and length(data.aws_availability_zones.available.names) means, if the first part is true, then use this. In other
  words, if preferred number of public subnets is null (Or not known) then set the value to the data returned by lenght function.
- The third part : and var.preferred_number_of_public_subnets means, if the first condition is false, i.e preferred number of public
  subnets is not null then set the value to whatever is definied in var.preferred_number_of_public_subnets

Now the entire configuration should now look like this

```bash
# Get list of availability zones
data "aws_availability_zones" "available" {
  state = "available"
}

variable "region" {
  default = "us-east-1"
}

variable "vpc_cidr" {
  default = "172.16.0.0/16"
}

variable "enable_dns_support" {
  default = "true"
}

variable "enable_dns_hostnames" {
  default = "true"
}

variable "preferred_number_of_public_subnets" {
  default = 2
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = var.enable_dns_support
  enable_dns_hostnames = var.enable_dns_hostnames
}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4, count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}

```

![image]()

![image]()

Note: You should try changing the value of preferred_number_of_public_subnets variable to null and notice how many subnets get
created.

> First, destroy the current infrastructure. Since we are still in development, this is totally fine. Otherwise, DO NOT DESTROY an
> infrastructure that has been deployed to production.

To destroy whatever has been created run terraform destroy command, and type yes after evaluating the plan.

```bash
terraform destroy
```
