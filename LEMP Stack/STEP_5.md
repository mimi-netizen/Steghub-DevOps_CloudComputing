# Testing PHP with Nginx

We can test to validate that nginx can correctly hand `.php` files off our PHP processor.  
We'll use `ifo.php` file

```bash
nano /var/www/projectLEMP/info.php
```

then

```bash
<?php
phpinfo();
```

We can now access this page in our web browser by visiting the domain name followed by `/info.php:`

```bash
http://16.171.154.194/info.php
```

and remove sensitive info through

```bash
sudo rm /var/www/projectLEMP/info.php
```

![image](image/Plemp.jpg)
