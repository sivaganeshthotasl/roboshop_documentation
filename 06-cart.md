**✅ Cart**

The Cart Service is a microservice that handles all cart-related operations in the RobotShop application. 

It is built using Node.js, and the required version should be confirmed with the developer. In this case, it supports Node.js version 20 or higher.

By default, Node.js 16 is available in the system. However, since the application requires a newer version, we need to enable Node.js 20 and install it.

You can list modules by using ```dnf module list nodejs -y```

1️⃣ Disable the default Node.js module

```
dnf module disable nodejs -y
```

2️⃣ Enable Node.js 20

```
dnf module enable nodejs:20 -y
```

3️⃣ Install Node.js

```
dnf install nodejs -y
```

4️⃣ Verify installation

```
node -v
```

🔹 Application Configuration

The Cart Service application is not provided as an RPM package like other software. So, we need to configure and set up the application manually.

🔹 Important Note

As discussed in Linux basics, applications should not be run as the root user. Instead, we should run the application using a separate non-root user for better security.

**Add application User**

```
useradd --system --home /app --shell /sbin/nologin --comment "roboshop system user" roboshop
```

he roboshop user is a dedicated system user used to run the application as a service. It is not used for logging into the server.

The username roboshop is chosen to match the project name and make it easy to identify. The application is stored in a standard location, which is a common practice in organizations for better management and consistency.

**Let’s create a directory to store the application files**

```
mkdir /app
```

**Download the application code into the created application directory**

```
curl -L -o /tmp/cart.zip https://roboshop-artifacts.s3.amazonaws.com/cart-v3.zip 
cd /app 
unzip /tmp/cart.zip
```

Every application developed by the team uses some common software libraries. These dependencies are defined in the application configuration file.

**Let’s install the required dependencies for the application**

```
cd /app
npm install
```

We need to create a new service in systemd so that it can be managed using systemctl.

As discussed in Linux basics, using systemctl to manage services provides several advantages such as easy start, stop, restart, and automatic startup.

**Configure a systemd service to manage the User Service application**

```
vi /etc/systemd/system/cart.service
```

```
[Unit]
Description = Cart Service
[Service]
User=roboshop
// highlight-start
Environment=REDIS_HOST=<REDIS-SERVER-IP>
Environment=CATALOGUE_HOST=<CATALOGUE-SERVER-IP>
Environment=CATALOGUE_PORT=8080
// highlight-end
ExecStart=/bin/node /app/server.js
SyslogIdentifier=cart

[Install]
WantedBy=multi-user.target
```

**Note: Environment variables are used to pass configuration values (like database URLs) to the application. They help avoid hardcoding, make changes easy, and allow the same code to run in different environments.**

Reload the systemd service so the new configuration is applied

```
systemctl daemon-reload
```

Enable & Start the cart Service using systemctl

```
systemctl enable cart
```

```
systemctl start cart
systemctl status cart
```






