# CI/CD Pipline for TODO Application

We already have **tooling** website as a part of deployment through Ansible. Here we will introduce another PHP application to add to the
list of software products we are managing in our infrastructure. The good thing with this particular application is that it has
unit tests, and it is an ideal application to show an end-to-end CI/CD pipeline for a particular application.

Our goal here is to deploy the application onto servers directly from Artifactory rather than from `git` If you have not updated
Ansible with an Artifactory role, simply use this guide to create an Ansible role for Artifactory (ignore the Nginx part).
[Configure Artifactory on Ubuntu 20.04](https://www.howtoforge.com/tutorial/ubuntu-jfrog/)

```bash
/path/to/your/laravel/project
├── app
│   ├── Console
│   ├── Exceptions
│   ├── Http
│   │   ├── Controllers
│   │   ├── Middleware
│   ├── Models
│   ├── Providers
├── bootstrap
│   ├── cache
├── config
│   ├── app.php
│   ├── database.php
│   └── ...
├── database
│   ├── factories
│   ├── migrations
│   ├── seeders
├── public
│   ├── index.php
│   ├── css
│   ├── js
│   ├── ...
├── resources
│   ├── js
│   ├── lang
│   ├── views
│   └── ...
├── routes
│   ├── api.php
│   ├── channels.php
│   ├── console.php
│   ├── web.php
├── storage
│   ├── app
│   ├── framework
│   ├── logs
├── tests
│   ├── Feature
│   ├── Unit
├── vendor
├── .env
├── artisan
├── composer.json
├── composer.lock
├── package.json
├── phpunit.xml
└── webpack.mix.js

```

**Prerequests**  Make sure port 8082 is opened in artifactory server

```
ansible-galaxy collection install jfrog.platform
```

![image](image/art.jpg)

Update `playbook/site.yml`

![](image/art1.jpg)

Update `inventory/ci.yml`

![](image/art2.jpg)

![](image/art3.jpg)

Run the playbook against `ci.yml` to install jfrog artifactory

![](image/x.jpg)

![](image/x1.jpg)

![](image/x2.jpg)

![](image/x3.jpg)

![](image/x4.jpg)

In the browser, login into artifactory with the default authentication `admin` and `password`

![image](image/q.jpg)

![image](image/q1.jpg)

Create a local repository `todo-dev-local`

![image](image/q2.jpg)

![image](image/q3.jpg)

### Phase 1 – Prepare Jenkins

1. Fork the repository below into your GitHub account

```
https://github.com/StegTechHub/php-todo.git
```

![image](image/f.jpg)

2. On you Jenkins server, install PHP, its dependencies and Composer tool (Feel free to do this manually at first, then update
   your Ansible accordingly later)

```
sudo apt update
```

**Install dependancies**

```
sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
```

**Install Composer** Download the Installer:

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

**Install Composer Globally**

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

**Remove the Installer**

```
php -r "unlink('composer-setup.php');"
```

**Verify Installation**

```
php -v
```

```
composer -v
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/98e98be5-ab3a-4c71-9e29-0b54b243e3f0)

3.  Install Jenkins plugins

        1 .  [Plot plugin](https://plugins.jenkins.io/plot/)

    ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2eec50c9-e487-48c2-9640-ea2f6e8c8d4f)

        2 .  [Artifactory plugin](https://www.jfrog.com/confluence/display/JFROG/Jenkins+Artifactory+Plug-in)

    ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1c750e6d-951e-4377-b192-34b5acca7a43)

- We will use _plot_ plugin to display tests reports, and code coverage information.
- The _Artifactory_ plugin will be used to easily upload code artifacts into an Artifactory server.

4. In Jenkins UI configure Artifactory

Configure the server ID, URL and Credentials, run Test Connection.

### Phase 2 – Integrate Artifactory repository with Jenkins

1.  Create a dummy Jenkinsfile in the todo app repository > In VScode create a new Jenkinsfile in the Todo repository

2.  Using Blue Ocean, create a multibranch Jenkins pipeline

3.  In jenkins server Install my sql client:

```
sudo apt install mysql-client -y
```

On the database server, create database and user

```
Create database homestead;
CREATE USER 'homestead'@'%' IDENTIFIED BY 'sePret^i';
GRANT ALL PRIVILEGES ON * . * TO 'homestead'@'%';
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4721ad96-9cbe-442b-beb7-749864a666c4)

Login into the DB-server(mysql server) and set the the bind address to 0.0.0.0:

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

Restart the my sql- server:

```
sudo systemctl restart mysql
```

4. Update the database connectivity requirements in the file .env.sample

```
DB_HOST=172.31.26.78
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=sePret^i
DB_CONNECTION=mysql
DB_PORT=3306
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/94fa0b61-6d14-4ddb-80d4-dc1cc849f176)

5. Update _Jenkinsfile_ with proper pipeline configuration

```
pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/melkamu372/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/28254d6d-1633-4850-8525-c4f940a67e62)

> **Notice the Prepare Dependencies section**

- The required file by PHP is _.env_ so we are renaming `.env.sample` to `.env`
- Composer is used by PHP to install all the dependent libraries used by the application
- php artisan uses the .env file to setup the required database objects

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4e7d79ed-e066-455b-a54c-3f5932902e75)

– After successful run of this step,
login to the database,password:`root`

```
mysql -u root -p
```

run show tables and you will see the tables being created for you
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f43b92e6-76f9-4833-a954-1f4cae6937aa)

1. Update the Jenkinsfile to include Unit tests step
   first install

```
composer require --dev phpunit/phpunit
```

then update Jenkinsfile

```
stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      }
```

### Phase 3 – Code Quality Analysis

This is one of the areas where developers, architects and many stakeholders are mostly interested in as far as product development
is concerned. As a DevOps engineer, you also have a role to play. Especially when it comes to setting up the tools.

For PHP the most commonly tool used for code quality analysis is [phploc](https://phpqa.io/projects/phploc.html).
[Read the article here for more](https://matthiasnoback.nl/2019/09/using-phploc-for-quick-code-quality-estimation-part-1/)

The data produced by **phploc** can be ploted onto graphs in Jenkins.

1. Add the code analysis step in Jenkinsfile. The output of the data will be saved in build/logs/phploc.csv file.

```
stage('Code Analysis') {
  steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'

  }
}
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8a0f302f-1e75-4462-bf5e-1b196df15414)

2. Plot the data using _plot Jenkins_ plugin.

This plugin provides generic plotting (or graphing) capabilities in Jenkins. It will plot one or more single values variations
across builds in one or more plots. Plots for a particular job (or project) are configured in the job configuration screen,
where each field has additional help information. Each plot can have one or more lines (called data series). After each build
completes the plots’ data series latest values are pulled from the CSV file generated by **phploc**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/21947c16-44bf-4500-a338-a1e19bc33bae)

You should now see a Plot menu item on the left menu. Click on it to see the charts. (The analytics may not mean much to you as
it is meant to be read by developers. So, you need not worry much about it – this is just to give you an idea of the real-world
implementation).
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0ea78af1-6e29-4a2d-95d9-f9f528981456) 3. Bundle the application code for into an artifact (archived package) upload to Artifactory

```
stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }

```

4. Publish the resulted artifact into Artifactory

```
stage ('Upload Artifact to Artifactory') {
          steps {
            script {
                 def server = Artifactory.server 'artifactory-server'
                 def uploadSpec = """{
                    "files": [
                      {
                       "pattern": "php-todo.zip",
                       "target": "<name-of-artifact-repository>/php-todo",
                       "props": "type=zip;status=ready"

                       }
                    ]
                 }"""

                 server.upload spec: uploadSpec
               }
            }

        }
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0c9bb61c-bc93-48b8-8e23-6e3cc35a4b28)

5. Deploy the application to the dev environment by launching Ansible pipeline

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-project/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```

The build job used in this step tells **Jenkins to start another job**. In this case it is the ansible-project job, and we are
targeting the main branch. Hence, we have ansible-project/main. Since the Ansible project requires parameters to be passed in,
we have included this by specifying the parameters section. The name of the parameter is env and its value is dev. Meaning,
deploy to the Development environment.
**Make sure to update artifactory login details in the todo deployment configuration file in ansible-config-mgt project**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/04f5ea5a-1980-409a-9c72-1630cf1f4ea5)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/04874304-1216-4ab7-83c9-1751f5556983)
Make sure zip is install

$ sudo apt install zip -y

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2612b0ec-88b1-4982-8f93-bea25fffc273)

But how are we certain that the code being deployed has the quality that meets corporate and customer requirements? Even though we
have implemented **Unit Tests** and **Code Coverage Analysis** with **phpunit** and **phploc**, we still need to implement **Quality Gate** to ensure
that ONLY code with the required code coverage, and other quality standards make it through to the environments.

To achieve this, we need to configure **SonarQube** – An open-source platform developed by **SonarSource for continuous inspection** of
code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.
