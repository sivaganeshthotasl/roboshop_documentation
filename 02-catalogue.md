**03-Catalogue**

**Catalogue is a microservice responsible for managing and providing the list of items (such as products or categories) that are displayed in the RoboShop application. It fetches this data from a database and serves it to other services or the frontend when requested.**

The developer has chosen Node.js. Check with the developer for the required version. The application works with Node.js version 20 and above.

**Install NodeJS, By default NodeJS 16 is available, We would like to enable 20 version and install list.**

**You can list modules by using**

```
dnf module list nodejs
```

**Disable current module**

```
dnf module disable nodejs -y

```

**Enable required module**

```
dnf module enable nodejs:20 -y
```

**Install NodeJS**

```
dnf install nodejs -y
```

**Configure the application**

This application is developed by our organization and does not come as an RPM package like other software. Therefore, we need to install and configure it manually step by step.

As discussed in the Linux basics section, applications should always run using a non-root user for better security.

**Add application User**

```
useradd --system --home /app --shell /sbin/nologin --comment "Roboshop system user" roboshop

👉 useradd roboshop

Creates a new user named roboshop

👉 --system

--> Creates a system user (not a normal login user)

--> Used for services/applications

--> Usually has no password & limited permissions

👉 --home /app

--> Sets home directory as /app

--> Your application files will live here

👉 --shell /sbin/nologin

--> This user cannot login to the server
--> No SSH access

✔ Security improvement

👉 --comment "Roboshop system user"

--> Just a description (for humans/admins)

```

✅ Simple meaning (one line)

👉 This command creates a secure, non-login user to run your application

**Note: "We create a system user like roboshop to run applications with limited privileges. Running applications as root is risky because any vulnerability can compromise the entire system. Using a dedicated user follows the principle of least privilege and improves security."**

**🔴Why shouldn't we run application as root?**

Root = full control of the system

**If your app runs as root:**

👉 Any bug or attack = full server compromise

⚠️ Example

**If attacker hacks your app:**

As root → can delete everything:

--> Can access all files

--> Can install malware

--> Can control entire server

**✅ Running as roboshop user**

**Now same attack:**

--> Limited permissions

--> Can access only /app

--> Cannot damage OS

--> Cannot touch other services

✔ Damage is contained

The username roboshop is chosen because it matches the project name.

We store the application in a standard location, as per the organization’s common practice.

**Lets setup an app directory**

```
mkdir /app
```

**Download the application code to created app directory and unzip the file from /app folder**

```
curl -o /tmp/catalogue.zip https://roboshop-artifacts.s3.amazonaws.com/catalogue-v3.zip 
cd /app
unzip /tmp/catalogue.zip
```

Every application developed by the development team uses some common software libraries. Similarly, this application also has its dependencies defined in the application configuration.

**Lets download the dependencies**

```
cd /app
npm install
```

**We need to setup a new service in systemd so systemctl can manage this service**

We already discussed in linux basics that advantages of systemctl managing services, Hence we are taking that approach. Which is also a standard way in the OS.

**Setup SystemD Catalogue Service**

**You can create file by using** 

```
vim /etc/systemd/system/catalogue.service
```

```
[Unit]
Description = Catalogue Service

[Service]
User=roboshop
Environment=MONGO=true
// highlight-start
Environment=MONGO_URL="mongodb://<MONGODB-SERVER-IPADDRESS>:27017/catalogue"
// highlight-end
ExecStart=/bin/node /app/server.js
SyslogIdentifier=catalogue

[Install]
WantedBy=multi-user.target
```

Ensure you replace ```<MONGODB-SERVER-IPADDRESS> ``` with the appropriate value. However, using an IP address is not recommended, as it may change over time. Instead, it is a best practice to use a domain name (DNS) for better flexibility and maintainability. 

example: ```mongodb.robossl.shop```

**Load the service**

```
systemctl daemon-reload
```

The above command is used because we added a new service. It reloads systemd so that it can detect the new service.


**Start the service**

```
systemctl enable catalogue
systemctl start catalogue
```

To make the application work correctly, we need to load the database schema and add initial data. Here, the initial data is the list of products.

Database schemas are usually part of the application code, and the developer provides them during development. Please follow the same approach for this application.

Master data are usually provided by business operations team.

To load schema / master data we need to install mongodb client and then we can load it.

To have mongo client installed we have to setup MongoDB repo and install mongodb-client. You can create file using

```
vim /etc/yum.repos.d/mongo.repo
```

```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
enabled=1
gpgcheck=0

```

Install the Mongodb-client

```
dnf install mongodb-mongosh -y
```

Load the master data, which includes the list of products we want to sell along with their quantity information.

```
mongosh --host MONGODB-SERVER-IPADDRESS </app/db/master-data.js
```

Note: Update the Catalogue service IP address or domain name (if configured in Route 53) in the frontend configuration file located at ```/etc/nginx/nginx.conf```

Use below commands to check data is loaded into mongodb or not Connect to MongoDB

```
mongosh --host MONGODB-SERVER-IPADDRESS

OR

mongosh --host MONGODB-DOMAIN-NAME
```

**Show databases**

```
show dbs
```

**Use database**

```
use database
```

**Show Collections**

```
show collections
```

**Get the items in collection**

```
db.products.fine()
```







