### 💸 Payment 💳 ###

This service handles all payment operations in the RoboShop e-commerce application.  
It is developed using Python 3.x, so Python must be installed to run this service.

The developer has used Python for this service. Please confirm with the developer which Python version is required.

**Install Python 3 and required build tools:**

```
dnf install python3 gcc python3-devel -y
```

```
python3         → Installs Python 3  
gcc             → Installs C compiler (needed for some Python packages)  
python3-devel   → Installs Python development files required for building modules  
-y              → Automatically confirms installation
```

**Configure the Application:**

This application is developed by our organization and does not provide an RPM package like other software.  
So, all setup and configuration steps must be done manually.

We already discussed in Linux basics section that applications should run as nonroot user.

```
useradd --system --home /app --shell /sbin/nologin --comment "roboshop system user" roboshop
```

The 'roboshop' user is a service (daemon) user used only to run the application. It is not intended for logging into the server.

The username 'roboshop' was chosen because it matches the project name.

The application is stored in a standard directory as per organizational practices.

**Create a directory for the application:**

```
mkdir /app
```

**Download the application source code into the created app directory:**

```
curl -L -o /tmp/payment.zip https://roboshop-artifacts.s3.amazonaws.com/payment-v3.zip 
cd /app 
unzip /tmp/payment.zip
```

Every application developed by the development team uses common software libraries. This application also defines its required dependencies in the application configuration.

**Install all required application dependencies defined in the configuration file:-**

```
cd /app 
pip3 install -r requirements.txt
```

**Download all required application dependencies defined in the configuration file:-**

```
cd /app 
pip3 install -r requirements.txt
```

Create a new systemd service so that systemctl can manage the application. 

As discussed in Linux basics, systemctl provides several advantages for managing services. We are using this standard approach since it is the recommended way in modern operating systems.

**Setup SystemD Payment Service:-**

Create Systemd Service File using :

```
vi /etc/systemd/system/payment.service
```

```
[Unit]
Description=Payment Service

[Service]
User=root
WorkingDirectory=/app
// highlight-start
Environment=CART_HOST=<CART-SERVER-IPADDRESS>
Environment=CART_PORT=8080
Environment=USER_HOST=<USER-SERVER-IPADDRESS>
Environment=USER_PORT=8080
Environment=AMQP_HOST=<RABBITMQ-SERVER-IPADDRESS>
// highlight-end
Environment=AMQP_USER=roboshop
Environment=AMQP_PASS=roboshop123

ExecStart=/usr/local/bin/uwsgi --ini payment.ini
ExecStop=/bin/kill -9 $MAINPID
SyslogIdentifier=payment

[Install]
WantedBy=multi-user.target
```

**Load the Service:-**

After creating the service file, reload systemd to register the new configuration:

```
systemctl daemon-reload
```

**Enable and Start the Service:-**

```
systemctl enable payment 
systemctl start payment
```




