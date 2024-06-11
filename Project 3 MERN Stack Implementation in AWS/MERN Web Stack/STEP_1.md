# Backend Configuration

Update ubuntu

```powershell
sudo apt update
```

![image](image/ubuntu-update.jpg)

Upgrade ubuntu

```powershell
sudo apt upgrade
```

![image](image/ubuntu-upgrade.jpg)

Get the location of Node.js software from ubuntu repositories.

```powershell
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

![image](image/node.jpg)

Install node.js and NPM

```powershell
sudo apt-get install -y nodejs
```

![image](image/nodejs.jpg)

Verify installation

```powershell
node -v
npm -v
```

![image](image/npm.jpg)

Let's create a new directory for our **TO-Do** project

```powershell
mkdir Todo
```

![image](image/Todo.jpg)

Now, we change our current directory to the newly created one

```powershell
cd Todo
```

![image](image/ctodo.jpg)

Then `npm init` and `ls` to confirm your package.json is installed

![image](image/init2.jpg)
![image](image/ls2.jpg)
