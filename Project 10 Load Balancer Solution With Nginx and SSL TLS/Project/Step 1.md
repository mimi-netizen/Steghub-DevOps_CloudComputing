# Step 1 - Load Balancer Solution With Nginx and SSL/TLS

This project uses the same infrastructure as project 8 but in this case, the load balancer solution will be configured with Nginx instead of Apache.

- Configure Nginx As A Load Balancer

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 - this port is used for secured HTTPS connections)

2. Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

3. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

![Screen Shot 2021-06-02 at 7 08 43 AM](https://user-images.githubusercontent.com/44268796/120470309-60f01680-c371-11eb-90c1-a668cca99f8b.png)

## Install Nginx

```
sudo apt update
sudo apt install nginx
```

Configure /etc/hosts on the Nginx Load Balancer server to add the web servers:

```
sudo nano /etc/hosts
```

![Screen Shot 2021-06-02 at 7 15 36 AM](https://user-images.githubusercontent.com/44268796/120471201-6c900d00-c372-11eb-9e37-4ffabd12ee08.png)

Next step is to configure the Load Balancer to point traffic to the DNS names of the two webservers:

```
sudo nano /etc/nginx/nginx.conf

#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
```

Restart Nginx and check status:

```
sudo systemctl restart nginx
sudo systemctl status nginx
```

![Screen Shot 2021-06-02 at 7 24 52 AM](https://user-images.githubusercontent.com/44268796/120472236-a1e92a80-c373-11eb-9a6d-65124ea928e0.png)
