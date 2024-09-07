# Configuring The Infrastructure With Ansible

- After a successful execution of terraform apply, connect to the bastion server through ssh-agent to run ansible against the infrastructure.

Run this commands to forward the ssh private key to the bastion server.

```bash
eval `ssh-agent -s`
ssh-add <private-key.pem>
ssh-add -l
```

- Update the `nginx.conf.j2` file to input the internal load balancer dns name generated.

![](image/ng.jpg)

- Update the `RDS endpoints`, `Database name`, `password` and `username` in the `setup-db.yml` file for both the `tooling` and `wordpress` role.

**For Tooling**

![](image/tool.jpg)

**For Wordpress**

![](image/serup.jpg)

- Update the `EFS` `Access point ID` for both the `wordpress` and `tooling` role in the `main.yml`

**For Tooling**

![](image/tooling.jpg)

**For Wordpress**

![](image/word.jpg)

### Access the bastion server with ssh agent

```bash
ssh -A ec2-user@<bastion-pub-ip>
```

Confirm ansible is installed on bastion server

![](image/bb.jpg)

- Verify the inventory

![](image/ansible-upgrade.jpg)

Export the environment variable `ANSIBLE_CONFIG` to point to the `ansible.cfg` from the repo and run the ansible-playbook command:

```bash
export ANSIBLE_CONFIG=/home/ec2-user/terraform-cloud/ansible/roles/ansible.cfg

ansible-playbook -i inventory/aws_ec2.yml playbook/site.yml
```

#### Access wordpress and tooling website via a browser

Tooling website

![](image/php.jpg)

Wordpress website

![](image/wordpress1.jpg)

![](image/wordpress-done.jpg)

# Practice Task №1

1. Configure 3 branches in the `terraform-cloud` repository for `dev`, `test`, `prod` environments

![](image/dev.jpg)

![](image/dev1.jpg)

![](image/branches.jpg)

2. Make necessary configuration to trigger runs automatically only for dev environment

- Create a workspace each for the 3 environments (i.e, `dev`, `test`, `prod`).

![](image/x.jpg)

- Configure `Auto-Apply` for `dev` workspace to trigger runs automatically

Go to the dev workspace in Terraform Cloud > Navigate to Settings > Vsersion Control > Check boxes for Auto Apply

![](image/vsc.jpg)

![](image/vsc1.jpg)

3. Create an Email and Slack notifications for certain events (e.g. `started plan` or `errored run`) and test it.

**Email Notification:** In the dev workspace, Go to Settings > Notifications > Add a new notification

![](image/notif.jpg)

![](image/notif1.jpg)

![](image/notif3.jpg)

The bastion instance type was changed to t3.small in order to test it

![](image/vb.jpg)

This will automatically apply after a successful plan

![](image/dev-b.jpg)

Confirm notification has bben sent to the provided email address

![](image/email.jpg)

![](image/email1.jpg)

![](image/email2.jpg)

### Slack Notification:

We will need to create a `Webhook URL` for the slack channel we want to send message to before creating the notification.

**Slack Notification Setup**

i. Visite [https://api.slack.com/apps?new_app=1](https://api.slack.com/apps?new_app=1).

ii. In the resulting popup, select Create an app > From scratch

iii. Choose a name > select the workspace you would like to send your notifications > Create App

![](image/slack.jpg)

iv. Click on `install to <Selected slack workspace name>` to install the Notification App

![](image/slack1.jpg)

v. Select Incoming Webhooks and copy the webhook URL.

![](image/slack2.jpg)

![](image/slack3.jpg)

Now, let's create the slack notification (paste the webhook url)

![](image/slack4.jpg)

![](image/slack5.jpg)

![](image/slack6.jpg)

The bastion instance type was changed back to t2.small in order to test it

![](image/slack7.jpg)

![](image/slack9.jpg)

Confirm the terraform process notification sent to the slack channel selected

![](image/slack8.jpg)

4. Apply destroy from Terraform Cloud web console.

![](image/slack10.jpg)

### Public Module Registry vs Private Module Registry

Terraform has a quite strong community of contributors (individual developers and 3rd party companies) that along with HashiCorp maintain a [Public Registry](https://developer.hashicorp.com/terraform/registry), where you can find reusable configuration packages ([modules](https://developer.hashicorp.com/terraform/registry/modules/use)). We strongly encourage you to explore modules shared to the public registry, specifically for this project - you can check out this [AWS provider registy page](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest).

As your Terraform code base grows, your DevOps team might want to create you own library of reusable components - [Private Registry](https://developer.hashicorp.com/terraform/registry/private) can help with that.

# Practice Task №2

## Working with Private repository

1. Create a simple Terraform repository (you can clone one [from here](https://github.com/hashicorp/learn-private-module-aws-s3-webapp)) that will be your module.

![](./images/module-repo.png)

- Under the repository's tab, clicking on `tag` to create tag. click `Create a new release` and adding `v1.0.0` to the tag version field setting the release title to `First module release`

![](./images/release-v1.png)

2. Import the module into your private registry

Go to Registery > Module > Add Module > select GitHub (Custom)

![](./images/add-module.png)

![](./images/publish-module.png)

Click on **`configure credentials`** from here

![](./images/bucket-webapp.png)

Click on `create an API toekn` from here

![](./images/create-api-token.png)

Configure the token generated, in the Terraform CLI configuration file `.terraformrc`.

```bash
vim ~/.terraformrc
```

Copy the credentials block below and paste it into the `.terraformrc` file.
Ensure to replace the value of the token argument with the API token created.

```bash
credentials "app.terraform.io" {
  # valid user API token
  token = "xxxxxxxxx.atlasv1.zzzzzzzzzzzzzzzzz"
}
```

Alternatively, we can choose to export the token using environment varaiabel in the CLI

```bash
export TERRAFORM_CLOUD_TOKEN="xxxxxxxxx.atlasv1.zzzzzzzzzzzzzzzzz"
```

3. Create a configuration that uses the module.

- In your local machine, create a new directory for the Terraform configuration.
  Create a `main.tf` file to use the module.
  Then click on `Copy configuration` under Usage instructions and paste it into main.tf

![](./images/usage-instruction.png)

![](./images/module-usage.png)

**Initialize the Configuration**

```bash
terraform init
```

![](./images/terraform-init.png)

4. Create a workspace for the configuration, Select CLI-driven workflow Name the workspace s3-webapp-workspace

![](./images/create-new-workspace.png)

![](./images/cli-driven.png)

Add the code block below to the terraform configuration file to setup the cloud integration.

```bash
terraform {
  cloud {

    organization = "celyne-project-19"

    workspaces {
      name = "s3-webapp-workspace"
    }
  }
}
```

![](./images/add-org-worksp.png)

5. Deploy the infrastructure

Run `terraform apply` to deploy the infrastructure.

![](./images/tform-apply.png)

![](./images/tf-cloud-run.png)

![](./images/s3.png)
![](./images/s3-object.png)

![](./images/game-website.png)

6. Destroy your deployment

Run `terraform destroy` to destory the infrastructure

![](./images/tf_destroy.png)

![](./images/tf-cloud-destroy.png)

## Conclusion

We have learned how to effectively use managed version of Terraform - `Terraform Cloud`. We have also practiced in finding modules in a Public Module Registry as well as build and deploy our own modules to a Private Module Registry.
