# Installing MySQL

```bash
sudo apt install mysql-server
```

![image](image/mysql_server.jpg)

When installation is finnished, we log into the mysql console by typing

```bash
sudo mysql
```

Then we set our password by

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```

The we exit  
mysql>exit
