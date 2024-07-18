# CONFIGURE SONARQUBE

We cannot run SonarQube as a root user, if you run using root user it will stop automatically. The ideal approach will be to create
a separate group and a user to run SonarQube

Create a group sonar

```
sudo groupadd sonar
```

Now add a user with control over the /opt/sonarqube directory

```
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R
```

Open SonarQube configuration file using your favourite text editor (e.g., nano or vim)

```
sudo vim /opt/sonarqube/conf/sonar.properties
```

Find the following lines:

```
#sonar.jdbc.username=
#sonar.jdbc.password=
```

Uncomment them and provide the values of PostgreSQL Database username and password:

```
#--------------------------------------------------------------------------------------------------

# DATABASE

#

# IMPORTANT:

# - The embedded H2 database is used by default. It is recommended for tests but not for

#   production use. Supported databases are Oracle, PostgreSQL and Microsoft SQLServer.

# - Changes to database connection URL (sonar.jdbc.url) can affect SonarSource licensed products.

# User credentials.

# Permissions to create tables, indices and triggers must be granted to JDBC user.

# The schema must be created first.

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

```

![](image/u5.jpg)

Edit the sonar script file and set RUN_AS_USER

```
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh
```

```
# If specified, the Wrapper will be run as the specified user.

# IMPORTANT - Make sure that the user has the required privileges to write

#  the PID file and wrapper.log files.  Failure to be able to write the log

#  file will cause the Wrapper to exit without any way to write out an error

#  message.

# NOTE - This will set the user which is used to run the Wrapper as well as

#  the JVM and is not useful in situations where a privileged resource or

#  port needs to be allocated prior to the user being changed.

RUN_AS_USER=sonar

```

![](image/u6.jpg)

Now, to start SonarQube we need to do following: Switch to sonar user

```
sudo su sonar
```

Move to the script directory

```
cd /opt/sonarqube/bin/linux-x86-64/
```

Run the script to start SonarQube

```
./sonar.sh start
```

Expected output shall be as:

```
Starting SonarQube...

Started SonarQube
```

Check SonarQube running status:

```
./sonar.sh status
```

Sample Output below:

```
./sonar.sh status

SonarQube is running (176483).
```

To check SonarQube logs, navigate to /opt/sonarqube/logs/sonar.log directory

```
tail /opt/sonarqube/logs/sonar.log
```

![](image/u7.jpg)

## Output

You can see that SonarQube is up and running

**Configure SonarQube to run as a systemd service**

Stop the currently running SonarQube service

```
cd /opt/sonarqube/bin/linux-x86-64/
```

Run the script to start SonarQube

```
./sonar.sh stop
```

Create a systemd service file for SonarQube to run as System Startup.

```
 sudo nano /etc/systemd/system/sonar.service
```

Add the configuration below for systemd to determine how to start, stop, check status, or restart the SonarQube service.

```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target

```

Save the file and control the service with systemctl

```
sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar
```

### We understand how mannually install SonarQube on Ubuntu 20.04 with PostgreSQL as the backend database here let us Launch a instance for sonarqube and configure the environemnt using ansible.,

sonarqube installation let us update our ansible config project

1. Set Up Inventory File:
   Define our target host(s) in an inventory file.
   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/35f60ef9-a4d7-46f4-ae7f-eb7b07d01c35)

2. Update Ansible Playbook:
   update a playbook that includes tasks for installing PostgreSQL, creating the SonarQube database and user, installing SonarQube, and configuring it to use PostgreSQL.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7905f67b-c350-4f81-8038-2edc7b223f69)

3. Update Role by adding role fo PostgreSQL and SonarQube :

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1f637fc0-af6d-49d4-bc61-7064ef5bb93c)

4. Execute the Playbook

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/59e83705-fa2f-42e9-9132-2f71be69e341)

**Access SonarQube** To access SonarQube using browser, type server’s IP address followed by port 9000

```
http://server_IP:9000 OR http://localhost:9000
```

Login to SonarQube with default administrator username and password – admin
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a9853c69-15c5-4aaa-9db5-b54d5027ad95)

Now, when SonarQube is up and running, it is time to setup our Quality gate in Jenkins.
