# MongoDB Database

First, we need an mLab account
![image](image/atlas.jpg)

After sign up, click on Create a project button then inside the project, click create Cluster button

![image](image/project.jpg)
![image](image/create-clusture.jpg)

It'll bring us to the Deploy your clusture page, Choose the free version, add a name, AWS cloud and then region closest to you then create deployment

![image](image/deploy.jpg)

Click connect clusture, then network access

![image](image/connect.jpg)

Change IP address to anywhere

![image](image/IP.jpg)

Create a database user

![image](image/create.jpg)
![image](image/created.jpg)

Create a Database User by going to the “Database Access” under the “Security” section.
Click on “Add New Database User”.

![image](image/user.jpg)

Enter a username and password. Ensure the “Read and Write to any database” option is selected.
Click "Add User".

![image](image/user-c.jpg)
![image](image/week.jpg)

Then go back to overview page, click in connect, select drivers and choose Mongoose , copy your conection url, click done.

![image](image/overview.jpg)
![image](image/mongodb.jpg)

Next, we create a file in our Todo and name it `.env`

```powershell
touch .env
vi .env
```

we paste in our connection string

```powershell
DB:mongodb+srv://<username>:<password>@steghub.mzlb1kj.mongodb.net/?retryWrites=true&w=majority&appName=StegHub
```

![image](image/a.jpg)

Navigate to the index.js file which i already set up,

     vim index.js

Clear out previous code and paste this sample :

```powershell
       const express = require('express');
       const bodyParser = require('body-parser');
       const mongoose = require('mongoose');
       const routes = require('./routes/api');
       const path = require('path');
       require('dotenv').config();

       const app = express();

       const port = process.env.PORT || 5000;

       //connect to the database
      mongoose.connect(process.env.DB)
     .then(() => console.log('Database connected successfully'))
     .catch(err => console.log('Error connecting to MongoDB:', err));


       //since mongoose promise is depreciated, we overide it with node's promise
       mongoose.Promise = global.Promise;

       app.use((req, res, next) => {
       res.header("Access-Control-Allow-Origin", "\*");
       res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
       next();
       });

       app.use(bodyParser.json());

       app.use('/api', routes);

       app.use((err, req, res, next) =>  {
       console.log(err);
       next();
       });

       app.listen(port, () =>  {
       console.log(`Server running on port ${port}`)
       });
```

![image](image/index.jpg)

Start the server `node index.js`  
You shall see a message "Database connected succesfully"

![image](image/connected.jpg)
