# Install Wordpress on your WebServer EC2

Update the instance

```powershell
    sudo yum -y update
```

Install wget apache , and all its dependencies

```powershell
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```

Enable, and start apache

```powershell
sudo systemctl enable httpd
sudo systemctl start httpd
```

![image](image/apache%20running.jpg)

Install php and all its dependencies

```powershell
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum module list php sudo yum module reset php

sudo yum module enable php:remi-7.4

sudo yum install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm setsebool -P httpd_execmem 1
```

Restart Apache

```powershell
sudo systemctl restart httpd
```

Create an info.php page to test if your configuration is correct

```powershell
 sudo vi /var/www/html/info.php
```

write the following code to check php config

```powershell
         <?php
        phpinfo();
            ?>
```

Visit your IPaddress/info.php
![image](image/info%20php%201.1.jpg)
![image](image/info%20php.jpg)

Download and Copy wordpress to the /var/www/html directory

```powershell
sudo wget http://wordpress.org/latest.tar.gz

sudo tar xzvf latest.tar.gz

sudo rm -rf latest.tar.gz

sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
```

configure SElinux policies

```powershell
sudo chown -R apache:apache /var/www/html/wordpress

sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R

sudo setsebool -P httpd_can_network_connect=1
```

![image](image/php%20wwordpress.jpg)
![image](image/apache.jpg)
