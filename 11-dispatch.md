### 🚚 Dispatch 🚛 ###

The Dispatch service handles sending products after a purchase. It is built using Go (Golang), so we need to install Go on the system.

Before installing, we should confirm the correct Go version with the developer.

**Install the required Go version:-**

```
dnf install golang -y
```

**Configure the application**

The application is developed by our team and is not available as an RPM package like other software. Therefore, we need to configure and set up everything manually.

As covered in Linux basics, applications should not run as the root user and must use a non-root user for better security.

**Create a dedicated user for running the application:-**

```
useradd --system --home /app --shell /sbin/nologin --comment "roboshop system user" roboshop
```

The roboshop user is a system (daemon) user used only to run the application. It is not used for logging into the server.

The username roboshop is chosen because it matches the project name and is easy to identify.

We store the application in a standard location. This is a common practice followed in organizations for consistency and easy management.

**Let’s create a dedicated application directory to store all application files in a structured way**

```
mkdir /app
```

Download the application source code into the application directory

```
curl -L -o /tmp/dispatch.zip https://roboshop-artifacts.s3.amazonaws.com/dispatch-v3.zip 
cd /app 
unzip /tmp/dispatch.zip
```

Every application developed by the team uses common software libraries. These dependencies are defined in the application configuration file.

Let’s install the required dependencies and build the application

```
cd /app 
go mod init dispatch
go get 
go build
```

👉 Setup project → ```go mod init```

👉 Download libraries → ```go get```

👉 Create executable → ```go build```


We need to create a systemd service so systemctl can manage it

As discussed in Linux basics, using systemctl to manage services has many advantages. So, we are following this approach, which is also the standard way in Linux operating systems.

**Set up a systemd service for the dispatch service:-**

```
vi /etc/systemd/system/dispatch.service
```

```
[Unit]
Description = Dispatch Service
[Service]
User=roboshop
// highlight-start
Environment=AMQP_HOST=RABBITMQ-IP
// highlight-end
Environment=AMQP_USER=roboshop
Environment=AMQP_PASS=roboshop123
ExecStart=/app/dispatch
SyslogIdentifier=dispatch

[Install]
WantedBy=multi-user.target
```

**Reload systemd to load the new service configuration**

```
systemctl daemon-reload
```

**Enable & Start the Shipping Service using systemctl**

```
systemctl enable dispatch 
systemctl start dispatch
```



