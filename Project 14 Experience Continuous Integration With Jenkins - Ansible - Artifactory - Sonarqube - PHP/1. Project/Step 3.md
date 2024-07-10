# ANSIBLE ROLES FOR CI ENVIRONMENT

Now go ahead and Add two more roles to ansible:

1. [SonarQube](https://www.sonarsource.com/products/sonarqube/) (Scroll down to the Sonarqube section to see instructions on how to
   set up and configure SonarQube manually)

2. [Artifactory](https://jfrog.com/artifactory/)

### Why do we need SonarQube?

**SonarQube** is an open-source platform developed by SonarSource for continuous inspection of code quality, it is used to perform
automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.
[Watch a short description here](https://youtu.be/vE39Fg8pvZg). There is a lot more hands on work ahead with SonarQube and Jenkins.
So, the purpose of SonarQube will be clearer to you very soon.

### Why do we need Artifactory?

**Artifactory** is a product by [JFrog](https://jfrog.com/) that serves as a binary repository manager. The binary repository is a natural extension to the
source code repository, in that the outcome of your build process is stored. It can be used for certain other automation,
but we will it strictly to manage our build artifacts.

[Watch a short description here](https://youtu.be/upJS4R6SbgM) Focus more on the first 10.08 mins

### Configuring Ansible For Jenkins Deployment

In previous projects, you have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible
from Jenkins UI.

**To do this**

## 1. Install Jenkins

Let's lunch a AWS ec2 with an Ubuntu OS instance and configure the jenkins server on it.

![]()

Install jenkins and it's dependencies using the terminal.

```bash
sudo apt-get update  # Update the instance

# Download jenkins key
sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -

# Add jenkins repository
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

# Add jenkins key
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5BA31D57EF5975CA

# Install Java
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt install openjdk-11-jdk

# Install Jenkins
sudo apt-get update
sudo apt-get install jenkins -y

# Enable and start Jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

```

![]()

### 1.2. Open TCP port 8080

![]()

### 1.3. Set up SSH-agent

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

```
eval `ssh-agent -s`
ssh-add <private key>.pem
ssh-add -l
ssh -A ubuntu@IP
```

## 2. Navigate to Jenkins URL

```
<Jenkins-server-Elastic-IP>:8080
```

## 3. Install & Open Blue Ocean Jenkins Plugin

In the Jenkins dashboard, click on **Manage Jenkins** -> **Manage plugins** and search for `Blue Ocean plugin`. Install and open Blue Ocean plugin

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/9d620074-9a78-422a-849e-476fde406990)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/26cf30d1-dd71-4571-9a7f-c5e2f2ca2893)

## 4. Create a new pipeline

## 5. Select GitHub

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/065e0b63-107d-49b9-af19-6d8b154af6e8)

## 6. Connect Jenkins with GitHub

## 7. Login to GitHub & Generate an Access Token

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d4e968fb-76ef-4c49-9bc5-3da0d99d23cc)

## 8. Copy Access Token

## 9. Paste the token and connect

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/71a801d0-eaa9-4cf7-b08b-c4bc5bd9b32f)

## 10. Create a new Pipeline

At this point you may not have a [Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/) in the Ansible repository, so
Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves.
So, click on Administration to exit the Blue Ocean console.

**Here is our newly created pipeline. It takes the name of your GitHub repository**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/411746d4-8a9c-48d1-8d57-6574f5d94d8e)

## Let us create our Jenkinsfile

In Vscode, inside the Ansible project, create a new directory and name it `deploy`, create a new file `Jenkinsfile` inside the directory

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/54966adb-61a4-45b3-9a09-4f5dbc30456c)

Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and
the only thing we are doing is using the shell script module to echo Building Stage

```bash
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8ece4b57-ce9a-455a-a963-1ab3111ddf92)

**Now go back into the Ansible pipeline in Jenkins, and select configure**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/bd9b9bd7-76be-4c66-a610-ff86bb3c5871)

**Scroll down to Build Configuration section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/824d9f98-aab9-49ad-a1a3-522bd0487db9)

**Back to the pipeline again, this time click `Build now`**

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/252b08d6-78aa-4906-8c76-b9219c2f7136)

This will trigger a build and you will be able to see the effect of our basic `Jenkinsfile` configuration by going through the console
output of the build.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0e83effc-f60d-4484-aab9-40b5167f297c)

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean
interface.

1. Click on Blue Ocean

2. Select your project

3. Click on the play button against the branch

   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/c6902703-5aef-4c59-a2b0-124f5ac3d0a3)

   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/cd29a156-f08d-49c5-a420-c3a7d60be659)

> Notice that this pipeline is a multibranch one. This means, if there were more than one branch in GitHub, Jenkins would have scanned
> the repository to discover them all and we would have been able to trigger a build for each branch.

**Let us see this in action**

1. Create a new git branch and name it `feature/jenkinspipeline-stages`

```bash
git checkout -b feature/jenkinspipeline-stages

```

2. Currently we only have the _Build stage_. Let us add another stage called _Test_. Paste the code snippet below and push the new changes
   to GitHub.

```bash
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/aa5602d3-735a-4483-923a-c07f76b07808)

4. To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

1. Click on the "Administration" button

1. Navigate to the Ansible project and click on `Scan repository now`

1. Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

   ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/984cbcc5-cde1-45c2-b539-c41f04e394fb)

1. In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a8826ed8-9215-4669-8cf3-ae8f3b0bda43)

### A QUICK TASK FOR YOU!

```bash
1. Create a pull request to merge the latest code into the main branch

2. After merging the PR, go back into your terminal and switch into the main branch.

3. Pull the latest change.

4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build
and test stages)

   1. Package

   2. Deploy

   3. Clean up
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f321a8cb-bbd5-4591-9d1b-f82d877cc9fd)

```bash
5. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch

6. Eventually, your main branch should have a successful pipeline like this in blue ocean
```

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/36bb0f6b-f402-4a6f-a156-4cedad8fd524)
