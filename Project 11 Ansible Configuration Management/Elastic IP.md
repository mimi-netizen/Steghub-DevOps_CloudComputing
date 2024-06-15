# Elastic IP

![image](image/OIP.jfif)

An Elastic IP address (EIP) is a static, public IP address that is designed to be reassigned to different AWS (Amazon Web Services) resources dynamically. An EIP allows binding a static IP address with a running Amazon EC2 instance, Amazon RDS database instance or Elastic Load Balancer. This allows preserving a static public IP address even if the underlying resources change.

## Key Features

- Persists until explicitly released, even if resource stops or terminates.

- Can be assigned to a single EC2 instance or load balancer elastically.

- Accessible within and between AWS regions after mapping to a VPC.

- Billed separately on an hourly basis when not attached to a resource.

- Useful for DNS hostname mappings, immutability and non-disruptive transfers.

## Benefits of Elastic IP

- Preserves static public IP for applications during instance changes.

- Allows failover capability between multiple instances or availability zones.

- Simplifies application migration between instances and regions.

- Facilitates non-disruptive transfers of public IPs between accounts.

- Maps public IP to private IP for access from internet to VPC resources.

- Useful for DNS configurations, SSL certificates and other static bindings.

## Common Uses

- Web servers and public-facing applications with static IP requirements.

- Load balancers for high availability architectures.

- Non-disruptive transfers during maintenance or account migrations.

- Static IP for SSH access, VPN gateways or database endpoints.

- IP address preservation during instance resizing, replacement or upgrades.

## Conclusion

Elastic IP addresses help overcome the dynamic nature of AWS resources by allowing preservation of public IP assignments. They enable non-disruptive transfers, high availability architectures and other use cases needing stable internet-facing endpoints on AWS infrastructure.
