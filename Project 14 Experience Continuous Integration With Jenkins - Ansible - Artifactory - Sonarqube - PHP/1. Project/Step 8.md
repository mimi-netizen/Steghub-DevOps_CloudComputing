# Configure SonarQube and Jenkins For Quality Gate

- In Jenkins, install [SonarScanner plugin](https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/jenkins-extension-sonarqube/)

  ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1e14fe3a-5706-4f82-9938-6ec90b322ac3)

- Navigate to configure system in Jenkins. Add SonarQube server as shown below: Manage **Jenkins** > **Configure System**

  ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3f64d116-a97a-4df4-8032-c94f4f15345e)

- Generate authentication token in SonarQube

  **User** > **My Account** > **Security** > **Generate Tokens**

  ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ea13e42b-365d-4d05-8852-2c1cf0052d55)

- Configure Quality Gate Jenkins Webhook in SonarQube – The URL should point to your Jenkins server
  http://{JENKINS_HOST}/sonarqube-webhook/

**Administration** > **Configuration** > **Webhook**s > **Create**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b0eb04ed-5390-4dcf-9636-2f97477a8fbf)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4c8b712c-06e3-4ebb-8900-f9dffc9dc02e)

- Setup SonarQube scanner from Jenkins – Global Tool Configuration

Manage Jenkins > Global Tool Configuration

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2978ac27-8ea3-43b2-a115-82fabaa7f9a6)

### Update Jenkins Pipeline to include SonarQube scanning and Quality Gate

Below is the snippet for a Quality Gate stage in Jenkinsfile

```
  stage('SonarQube Quality Gate') {
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
            }

        }
    }
```

> NOTE: The above step will fail because we have not updated `sonar-scanner.properties`

- Configure sonar-scanner.properties – From the step above, Jenkins will install the scanner tool on the Linux server. You will need
  to go into the tools directory on the server to configure the properties file in which SonarQube will require to function during
  pipeline execution.

```
cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/conf/
```

Open sonar-scanner.properties file

```
sudo vi sonar-scanner.properties
```

Add configuration related to `php-todo` project

```
sonar.host.url=http://<SonarQube-Server-IP-address>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml

```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/22a2f1b6-6665-4d23-834c-22a3c0314591)

> HINT: To know what exactly to put inside the `sonar-scanner.properties` file, SonarQube has a configurations page where you can get
> some directions.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7e77f4e7-435c-45f8-bc36-cbe3d9b35cd5)

A brief explanation of what is going on the the stage – set the environment variable for the scannerHome use the same name used
when you configured SonarQube Scanner from Jenkins **Global Tool Configuration**. If you remember, the name was SonarQubeScanner.
Then, within the steps use shell to run the scanner from `bin` directory.

To further examine the configuration of the scanner tool on the Jenkins server – navigate into the tools directory

```
cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/bin
```

List the content to see the scanner tool `sonar-scanner`. That is what we are calling in the pipeline script.

Output of (ls -latr)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/786257c6-4420-4f49-a109-f393a1ed85fb)

So far you have been given code snippets on each of the stages within the Jenkinsfile. But, you should also be able to generate
Jenkins configuration code yourself.

- To generate Jenkins code, navigate to the dashboard for the php-todo pipeline and click on the Pipeline Syntax menu item

```
**Dashboard** > **php-todo** > Pipeline Syntax
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9a412e50-6268-4b93-866d-5bb1744e080a)

- Click on Steps and select withSonarQubeEnv – This appears in the list because of the previous SonarQube configurations you
  have done in Jenkins. Otherwise, it would not be there.
  ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1e104c75-319a-48f4-8e8f-f4ae1509b6b0)

Within the generated block, you will use the sh command to run shell on the server. For more advanced usage in other projects,
you can add to bookmarks this
[SonarQube documentation page](https://docs.sonarqube.org/latest/analyzing-source-code/scanners/jenkins-extension-sonarqube/)
in your browser.

### End-to-End Pipeline Overview

Indeed, this has been one of the longest projects from Project 1, and if everything has worked out for you so far, you should have
a view like below:
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4890b539-32c9-4fa8-9655-e63050996975)

**But we are not completely done yet!**

The quality gate we just included has no effect. Why? Well, because if you go to the SonarQube UI, you will realise that we just
pushed a poor-quality code onto the development environment.

- Navigate to php-todo project in SonarQube

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f6d68924-aae4-4c5d-a84e-f05c543f6dad)

There are bugs, and there is 0.0% code coverage. (code coverage is a percentage of unit tests added by developers to test functions
and objects in the code)

- If you click on php-todo project for further analysis, you will see that there is 6 hours’ worth of technical debt, code smells
  and security issues in the code.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/afeef638-16c7-43c8-8af9-c9dbd98c93be)

In the development environment, this is acceptable as developers will need to keep iterating over their code towards perfection.
But as a DevOps engineer working on the pipeline, we must ensure that the quality gate step causes the pipeline to fail if the
conditions for quality are not met.

### Conditionally deploy to higher environments

In the real world, developers will work on feature branch in a repository (e.g., GitHub or GitLab). There are other branches that
will be used differently to control how software releases are done. You will see such branches as:

1. Develop
2. Master or Main
   (The \* is a place holder for a version number, Jira Ticket name or some description. It can be something like Release-1.0.0)
3. Feature/\*
4. Release/\*
5. Hotfix/\*
   etc.

There is a very wide discussion around release strategy, and git branching strategies which in recent years are considered under
what is known as GitFlow (Have a read and keep as a bookmark – it is a possible candidate for an interview discussion, so take it
seriously!)

Assuming a basic `gitflow` implementation restricts only the develop branch to deploy code to Integration environment like sit.

Let us update our Jenkinsfile to implement this:

- First, we will include a When condition to run Quality Gate whenever the running branch is either develop, hotfix, release, main,
  or master

```
when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
```

Then we add a timeout step to wait for SonarQube to complete analysis and successfully finish the pipeline only when code quality
is acceptable.

```
 timeout(time: 1, unit: 'MINUTES') {
        waitForQualityGate abortPipeline: true
    }
```

The complete stage will now look like this:

```
 stage('SonarQube Quality Gate') {
      when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
            }
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }

```

To test, create different branches and push to GitHub. You will realise that only branches other than develop, hotfix, release,
main, or master will be able to deploy the code.

**Create a new branch feature/sonar-test and commit, push the new code.**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0c09806f-08b8-4091-8bc0-64426dc02461)

If everything goes well, you should be able to see something like this:

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c8d7e050-c026-403a-9dd4-0fb242f24c29)

For Branch feature/sonar-test
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b84fdf2a-e79e-4c16-a64e-ef1222053de9)

For main branch

> Notice that with the current state of the code, it cannot be deployed to Integration environments due to its quality.
> In the real world, DevOps engineers will push this back to developers to work on the code further, based on SonarQube quality report.
> Once everything is good with code quality, the pipeline will pass and proceed with sipping the codes further to a higher environment.

### Complete the following tasks to finish Project 14

1. Introduce Jenkins agents/slaves – Add 2 more servers to be used as Jenkins slave. Configure Jenkins to run its pipeline jobs
   randomly on any available slave nodes.
   Let's add 2 more servers to be used as Jenkins slave and install java in them.  
   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/ac716691-59fb-405f-9a9e-4cd9311b23f6)

```
sudo apt update
sudo apt install default-jdk
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7a4645c8-bb8d-4950-8772-c2e73d62d10f)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f0fe6d73-12ac-40ae-9b08-e52c4bf39bb1)

2. Configure webhook between Jenkins and GitHub to automatically run the pipeline when there is a code push. Let's Configure the new nodes on Jenkins Server.
   Navigate to **Dashboard** > **Manage Jenkins** > **Nodes**, click on New node and enter a Name and click on create.
   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/becc5848-9f29-4366-a155-d2c95043798c)

**To connect to slave_one completed this fields and save.**

Name: slave_one
Remote root directory: /opt/build (This can be any directory for the builds)
Labels: slave_one
save
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/906af197-bb70-4a84-aeb7-120aa4a5b8f5)

To connect to slave_one, click on the slave_one and if you finsih configuration save it you see
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fc124386-2e2d-4901-8ab4-ac9c6553fa08)

Use either options. In this case, I use the first option

> befor running please check yor public ip addres of your jenkin is same as the if not go to Dashboard> manage Jenkin > systme and update current IP

```
sudo mkdir -p /opt/build
sudo chown -R ubuntu:ubuntu /opt/build
sudo chmod -R 755 /opt/build
ls -ld /opt/build
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a0096577-6378-44ef-8656-956f24d23a87)

**To make it run in background and `&`**

```
java -jar agent.jar -webSocket -url http://50.17.45.184:8080/ -secret @secret-file -name "slave_one" -workDir "/opt/build" &

```

**Repate same thing for the second**
slave_one

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1f21c131-8a7e-4c43-aae2-386512955e0a)

slave_two
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a735f3a4-ccfa-4692-b171-2dbe909d4490)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2dca8f1d-ff4c-43f6-92d2-a3f742de9839)

3. Deploy the application to all the environments in order to deploy to all environment we Add these stages to our existing Jenkins pipeline script

**Development**

```
stage ('Deploy to Dev Environment') {
            agent { label 'slave_one' } // Specify the Jenkins slave to use for deployment
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
            }
        }
```

**Test Environment**

```
        stage ('Deploy to Test Environment') {
            agent { label 'slave_two' } // Specify another Jenkins slave for deployment
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'pentest']], propagate: false, wait: true
            }
        }
```

**Production Environment**

```
        stage ('Deploy to Production Environment') {
            agent any
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'ci']], propagate: false, wait: true
            }
        }
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2e0e4713-1b71-4185-a55a-d7a658fc8671)

**Running on slave console Output**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f19fcc0a-ce9c-443d-b598-9179eda33fa8)

4. **Optional** – Experience pentesting in pentest environment by configuring [Wireshark](https://www.wireshark.org/) there and just explore for information sake only.[Watch Wireshark Tutorial here](https://youtu.be/lb1Dw0elw0Q)

- Ansible Role for Wireshark:
- https://github.com/ymajik/ansible-role-wireshark (Ubuntu)
- https://github.com/wtanaka/ansible-role-wireshark (RedHat)
  ### The End of Project 14
  We have just experienced one of the most interesting and complex projects in our Project Based Learning journey
  so far.
