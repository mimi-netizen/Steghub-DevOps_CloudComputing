# RUN FIRST ANSIBLE TEST

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

```
cd ansible-config-mgt
```

```
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```

![image](image/7.1.jpg)
![image](image/7.2.jpg)
![image](image/e1.jpg)
![image](image/e2.jpg)
![image](image/e3.jpg)

You can go to each of the servers and check if wireshark has been installed by running `which wireshark` or `wireshark --version`

#### NFS

![image](image/nfs.jpg)

#### Webserver 1

![image](image/w1.jpg)

#### Webserver 2

![image](image/w2.jpg)

#### lb

![image](image/lb.jpg)

#### db

![image](image/db.jpg)

Your updated with Ansible architecture now looks like this:

![6038](https://user-images.githubusercontent.com/85270361/210154593-092a4ee2-ab8b-4212-a260-8845c3f8693a.PNG)

## Optional step - Repeat once again

Update your ansible playbook with some new Ansible tasks and go through the full
checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a
servers fleet of any size with just one command!

Congratulations
You have just automated your routine tasks by implementing your first Ansible project! There is more exciting projects ahead, so lets
keep it moving!

![6039](https://user-images.githubusercontent.com/85270361/210154612-a005616b-7e5d-4b33-8ed2-6723aab0837c.PNG)
