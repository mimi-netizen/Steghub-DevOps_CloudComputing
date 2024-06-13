## CONFIGURE NGINX AS A LOAD BALANCER

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses
- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

```
sudo apt update
sudo apt install nginx
```

- Open the default nginx configuration file

`sudo vi /etc/nginx/nginx.conf`

```
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

![image](https://user-images.githubusercontent.com/71001536/165980865-0676b91b-e4ae-4360-989d-3094d402d0bf.png)

![image](https://user-images.githubusercontent.com/71001536/165980809-c6b96ba5-6850-450a-a712-884fa5f380a2.png)

![image](https://user-images.githubusercontent.com/71001536/165981060-70af1d35-57a3-46dd-9e6a-8bc60a5fc5d3.png)

![image](https://user-images.githubusercontent.com/71001536/165981267-1ee2f60e-340a-4681-a1bf-d88692378091.png)

![image](https://user-images.githubusercontent.com/71001536/165981343-d11cd415-c66c-4478-8320-c9adb181b6f5.png)

- Install certbot and request for an SSL/TLS certificate, Make sure snapd service is active and running

`sudo systemctl status snapd`

- Install certbot

`sudo snap install --classic certbot`

- To activate the ssl certificate

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

![image](https://user-images.githubusercontent.com/71001536/166066051-9ec80a42-d534-412d-9392-7b060be11a27.png)

![image](https://user-images.githubusercontent.com/71001536/166066173-81d8b863-ac7c-4a25-a28b-393e5036a683.png)

- Set up periodical renewal of your SSL/TLS certificate
- By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.
- You can test renewal command in dry-run mode

`sudo certbot renew --dry-run`

- To do so, lets edit the crontab file with the following command:

`crontab -e`

- Add following line:

`* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`
