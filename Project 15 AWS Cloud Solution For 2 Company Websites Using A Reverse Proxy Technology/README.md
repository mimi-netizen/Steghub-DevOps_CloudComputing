# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

WARNING: This infrastructure set up is NOT covered by AWS free tier. Therefore, ensure to DELETE ALL the resources created immediately
after finishing the project. Monthly cost may be shockingly high if resources are not deleted. Also, it is strongly recommended to
set up a budget and configure notifications when your spendings reach a predefined limit. Watch this video to learn how to configure
AWS budget.

Let us Get Started
You have been doing great work so far – implementing different Web Solutions and getting hands on experience with many great DevOps
tools. In previous projects you used basic [Infrastructure as a Service (IaaS)](https://en.wikipedia.org/wiki/Infrastructure_as_a_service)
offerings from AWS such as [EC2 (Elastic Compute Cloud)](https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud) as rented
Virtual Machines and [EBS (Elastic Block Store)](https://en.wikipedia.org/wiki/Amazon_Elastic_Block_Store), you have also learned how
to configure Key pairs and basic Security Groups.

But the power of Clouds is not only in being able to rent Virtual Machines – it is much more than that. From now on, you will start
gradually study different Cloud concepts and tools on example of AWS, but do not be worried, your knowledge will not be limited to
only AWS specific concepts – overall principles are common across most of the major Cloud Providers (e.g., Microsoft Azure and
Google Cloud Platform).

NOTE: The next few projects will be implemented manually. Before you begin to automate infrastructure in the cloud, it is very
important that you can build the solution manually. Otherwise, programming your automation may become frustrating very quickly.

You will build a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a fictitious company (Choose an interesting
name for it) that uses [WordPress CMS](https://wordpress.com/) for its main business website, and a Tooling Website
(https://github.com/<your-name>/tooling) for their DevOps team. As part of the company’s desire for improved security and performance,
a decision has been made to use a reverse proxy technology from NGINX to achieve this.

Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below,
ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accomodate to
increased traffic and, at the same time, has reasonable cost.

![6092](https://user-images.githubusercontent.com/85270361/210170903-ae774e7d-443e-46dd-baeb-65ecf1c20d81.PNG)
