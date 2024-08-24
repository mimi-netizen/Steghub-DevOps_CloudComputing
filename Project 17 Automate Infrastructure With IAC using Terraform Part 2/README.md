# Automate Infrastructure With IAC using Terraform Part 2

Before we go deeper into automating other parts of our infrastructure on AWS, it is very important to fully understand certain concepts
around Networking (in case this is completely new area to you). Networking is a very broad topic and some of internals of Terraform
modules related to Networking, like cidrsubnet(), may still not be fully clear to you.

To fully clear your understanding, we highly recommend you watching Networking videos by Eli the Computer Guy on YouTube, and in
addition to that, read following golden articles on Networking Terminology, Interfaces, Protocols, IP Address, Subnets, and CIDR
Notation by Justin Ellingwood from Digital Ocean.

Eli the Computer Guy videos

- [Introduction to Networking](https://youtu.be/rL8RSFQG8do)
- [TCP/IP and Subnet Masking](https://youtu.be/EkNq4TrHP_U)

If you are interested to dive deeper into Networking domain, you can watch the entire playlist
[here](https://www.youtube.com/playlist?list=PLF360ED1082F6F2A5)

Justin Ellingwood blog posts
**WARNING:** You may initially feel overwhelmed by the information provided in these articles. It is fine if you don not fully understand
them the first time. Bookmark the pages, read them again and again for the next few days. Find other articles talking about the same
topics on Google and watch YouTube videos about them. Subconsciously, you will begin to understand them over time.

- [Networking Part 1](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols)
- [Networking Part 1](https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking#netmasks-and-subnets)
