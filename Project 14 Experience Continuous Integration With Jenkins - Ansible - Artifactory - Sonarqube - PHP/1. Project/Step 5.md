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

![image](image/x5.jpg)

3.  Install Jenkins plugins

        1 .  Plot plugin

    ![image](image/q4.jpg)

        2 .  Artifactory plugin

    ![image](image/q5.jpg)

- We will use _plot_ plugin to display tests reports, and code coverage information.
- The _Artifactory_ plugin will be used to easily upload code artifacts into an Artifactory server.

4. In Jenkins UI configure Artifactory

Configure the server ID, URL and Credentials, run Test Connection.

![](image/q6.jpg)

![](image/q7.jpg)

### Phase 2 – Integrate Artifactory repository with Jenkins

1.  Create a dummy Jenkinsfile in the todo app repository > In VScode create a new Jenkinsfile in the Todo repository:

    1.1 First step, Fork the TODO repository below into your GitHub account.

        `https://github.com/StegTechHub/php-todo.git`

    1.2 Create a basic Jenkinsfile in the root of the TODO application repository. This file will define your pipeline stages.

```bash
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
            git branch: 'main', url: 'https://github.com/mimi-netizen/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```

![](image/q8.jpg)

2.  Using Blue Ocean, create a multibranch Jenkins pipeline

- Use Blue Ocean in Jenkins to create a new multibranch pipeline.

![](image/z.jpg)

- Connect it to your GitHub repository where the TODO application is forked.

![](image/z1.jpg)

3.  In jenkins server Install my sql client:

```
sudo apt install mysql-client -y
```

![image](image/z2.jpg)

On the database server, create database and user

- On your database server, create the necessary database and user as specified in the TODO application `.env`.

```
Create database homestead;
CREATE USER 'homestead'@'%' IDENTIFIED BY 'sePret^i';
GRANT ALL PRIVILEGES ON * . * TO 'homestead'@'%';
```

![image](image/q9.jpg)

Login into the DB-server(mysql server) and set the the bind address to 0.0.0.0:

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

![](image/z3.jpg)

Restart the my sql- server:

```
sudo systemctl restart mysql
```

![](image/z4.jpg)

4. Create a `.env` file in TODO repo and update it with the credentials to connect the database, use sample the code below :

```bash
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString
APP_URL=http://localhost

DB_HOST=172.31.8.236
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=sePret^i

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_DRIVER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

![image](image/t6.jpg)

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
            git branch: 'main', url: 'https://github.com/mimi-netizen/php-todo.git'
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

> **Notice the Prepare Dependencies section**

- Composer is used by PHP to install all the dependent libraries used by the application
- php artisan uses the .env file to setup the required database objects

_Note_ : The php version installed by the composer is 8.3.6 while the php version of the todo application is 7.4. Ensure to remove the current version and install php 7.4 and its dependencies to avoid error.

5. Run the TODO Pipeline:

![image](image/x6.jpg)

– After successful run of this step,
login to the database,password:`root`

```
mysql -u root -p
```

run show tables and you will see the tables being created for you

![image](image/db2.jpg)

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

![](image/xn.jpg)

### Phase 3 – Code Quality Analysis

This is one of the areas where developers, architects and many stakeholders are mostly interested in as far as product development
is concerned. As a DevOps engineer, you also have a role to play. Especially when it comes to setting up the tools.

For PHP the most commonly tool used for code quality analysis is [phploc](https://phpqa.io/projects/phploc.html).
[Read the article here for more](https://matthiasnoback.nl/2019/09/using-phploc-for-quick-code-quality-estimation-part-1/)

The data produced by **phploc** can be ploted onto graphs in Jenkins.

- First, install phploc and phpunit on Jenkins Server.

```bash
# Install phploc
wget -O phploc.phar https://phar.phpunit.de/phploc-6.0.0.phar

# Add the permission to make file executeable
chmod +x phploc.phar

# Reolad Shell Config
sudo mv phploc.phar /usr/local/bin/phploc

# verify the installation
phploc --version

# Download the phpunit PHAR file
wget -O phpunit.phar https://phar.phpunit.de/phpunit-9.5.10.phar

# Make the PHAR file executable
chmod +x phpunit.phar

# Move the PHAR file to a directory in your PATH
sudo mv phpunit.phar /usr/local/bin/phpunit

#Verify the installation
phpunit --version
```

![](image/xn1.jpg)

1. Add the code analysis step in Jenkinsfile. The output of the data will be saved in `build/logs/phploc.csv` file.

```
stage('Code Analysis') {
  steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'

  }
}
```

![image](image/xn2.jpg)

2. Plot the data using _plot Jenkins_ plugin.

This plugin provides generic plotting (or graphing) capabilities in Jenkins. It will plot one or more single values variations
across builds in one or more plots. Plots for a particular job (or project) are configured in the job configuration screen,
where each field has additional help information. Each plot can have one or more lines (called data series). After each build
completes the plots’ data series latest values are pulled from the CSV file generated by **phploc**

```bash
stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'plot-loc.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of Code', yaxis: 'Lines of Code'
                plot csvFileName: 'plot-structures.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
                plot csvFileName: 'plot-avg-length.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
                plot csvFileName: 'plot-complexity.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'
                plot csvFileName: 'plot-classes.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
                plot csvFileName: 'plot-methods.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
                plot csvFileName: 'plot-constants.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
                plot csvFileName: 'plot-testing.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
                plot csvFileName: 'plot-lloc.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
                plot csvFileName: 'plot-functions.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
                plot csvFileName: 'plot-objects.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'
            }
        }
```

![image](image/xn3.jpg)

You should now see a Plot menu item on the left menu. Click on it to see the charts. (The analytics may not mean much to you as
it is meant to be read by developers. So, you need not worry much about it – this is just to give you an idea of the real-world
implementation).

![image](image/xn4.jpg)

![image](image/xn5.jpg)

![image](image/xn6.jpg)

3. Bundle the application code for into an artifact (archived package) upload to Artifactory

```bash
stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }

```

4. Publish the resulted artifact into Artifactory

```bash
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
                println "Upload Spec: ${uploadSpec}"
                                  try {
                                      server.upload spec: uploadSpec
                                      println "Upload successful"
                                  } catch (Exception e) {
                                      println "Upload failed: ${e.message}"
                                    }
            }
          }


}
```

![image](image/qq.jpg)

![image](image/qq1.jpg)

![image](image/qq2.jpg)

- Confirm Artifact was Uploaded into JFrog local repo.

![](image/zip.jpg)

5. Deploy the application to the dev environment by launching Ansible pipeline

```bash
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

- Create a task to set up the Dev environment and deploy artifact to webserver (todo server)

```bash
# # tasks file for webserver - php-todo
---
# Install Apache
- name: Install Apache
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name: httpd
    state: present

# Install EPEL release
- name: Install EPEL release
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name: https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
    state: present

# Install dnf-utils and Remi repository
- name: Install dnf-utils and Remi repository
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name:
      - dnf-utils
      - http://rpms.remirepo.net/enterprise/remi-release-9.rpm
    state: present

# Reset PHP module
- name: Reset PHP module
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: dnf module reset php -y

# Enable PHP 7.4 module
- name: Enable PHP 7.4 module
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: dnf module enable php:remi-7.4 -y

# Install PHP and extensions
- name: Install PHP and extensions
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name:
      - php
      - php-opcache
      - php-gd
      - php-curl
      - php-mysqlnd
      - php-common
      - php-mbstring
      - php-intl
      - php-xml
      - php-fpm
      - php-json
    state: present
    enablerepo: remi-7.4

# Ensure PHP-FPM service is started and enabled
- name: Ensure PHP-FPM service is started and enabled
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: php-fpm
    state: started
    enabled: true

# Set SELinux boolean for httpd_execmem
- name: Set SELinux boolean for httpd_execmem
  remote_user: ec2-user
  become: true
  ansible.builtin.seboolean:
    name: httpd_execmem
    state: true
    persistent: yes

# Ensure httpd service is started and enabled
- name: Ensure httpd service is started and enabled
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: started
    enabled: true

# Download the artifact
- name: Download the artifact
  remote_user: ec2-user
  become: true
  ansible.builtin.get_url:
    url: http://52.52.188.173:8082/artifactory/todo-dev-local/php-todo.zip
    dest: /home/ec2-user/php-todo.zip
    url_username: admin
    url_password: <password>

- name: Ensure unzip is installed
  become: yes
  ansible.builtin.yum:
    name: unzip
    state: present
  when: ansible_os_family == "RedHat"

- name: Unzip the artifacts
  ansible.builtin.unarchive:
    src: /home/ec2-user/php-todo.zip
    dest: /var/www/html/
    remote_src: yes

# Unzip the artifacts
- name: Unzip the artifacts
  ansible.builtin.unarchive:
    src: /home/ec2-user/php-todo.zip
    dest: /home/ec2-user/
    remote_src: yes
  become: true

# Run chown on /var/www/html
- name: Run chown on /var/www/html
  ansible.builtin.command:
    cmd: chown -R $USER:$USER /var/www/html
  become: true

# Deploy the code
- name: Deploy the code
  remote_user: ec2-user
  become: true
  ansible.builtin.copy:
    src: /home/ec2-user/var/lib/jenkins/workspace/php-todo-pipeline/.
    dest: /var/www/html/
    force: yes
    remote_src: yes

# Delete the zip file after unzipping
- name: Delete the zip file after unzipping
  ansible.builtin.file:
    path: /home/ec2-user/php-todo.zip
    state: absent
  become: true

# Delete the unzipped files after copying
- name: Delete the unzipped files after copying
  ansible.builtin.file:
    path: /home/ec2-user/php-todo/
    state: absent
  become: true

# Remove Apache default welcome page on RedHat-based systems
- name: Remove Apache default welcome page
  remote_user: ec2-user
  become: true
  ansible.builtin.file:
    path: /etc/httpd/conf.d/welcome.conf
    state: absent
  when: ansible_os_family == "RedHat"

# Restart httpd
- name: Restart httpd
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: restarted

# Verify PHP version
- name: Verify PHP version
  remote_user: ec2-user
  become: true
  ansible.builtin.command: php -v
  register: php_version

# Display PHP version
- name: Display PHP version
  remote_user: ec2-user
  become: true
  ansible.builtin.debug:
    msg: "{{ php_version.stdout }}"

# Set SELinux context for web directory and PHP files
- name: Set SELinux context for web directory
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chcon -R -t httpd_sys_content_t /var/www/html

- name: Set SELinux context for PHP writable files
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chcon -R -t httpd_sys_rw_content_t /var/www/html

# Generate a new APP_KEY if missing or invalid
- name: Generate a new APP_KEY if missing or invalid
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: php artisan key:generate
  args:
    chdir: /var/www/html

# Clear the configuration cache
- name: Clear the configuration cache
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: php artisan config:cache
  args:
    chdir: /var/www/html

# Ensure .env file and config directory have correct permissions
- name: Ensure .env file and config directory have correct permissions - chown
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chown -R apache:apache /var/www/html/

- name: Ensure .env file and config directory have correct permissions - chmod
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chmod -R 775 /var/www/html/

# Restart the web server and PHP-FPM
- name: Restart the web server
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: restarted

- name: Restart PHP-FPM
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: php-fpm
    state: restarted
```

Make sure zip is installed

```bash
$ sudo apt install zip -y
```

![image](image/success.jpg)

But how are we certain that the code being deployed has the quality that meets corporate and customer requirements? Even though we
have implemented **Unit Tests** and **Code Coverage Analysis** with **phpunit** and **phploc**, we still need to implement **Quality Gate** to ensure
that ONLY code with the required code coverage, and other quality standards make it through to the environments.

To achieve this, we need to configure **SonarQube** – An open-source platform developed by **SonarSource for continuous inspection** of
code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.
