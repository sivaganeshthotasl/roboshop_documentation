### 🚚 Shipping ###

The Shipping Service calculates the distance for a package and determines the shipping cost based on that distance.

The Shipping Service is written in Java. Therefore, we need to install Java to run the application.

**🔹Maven Overview**

Maven is a build and packaging tool used for Java applications. It helps in compiling the code, managing dependencies, and creating application packages.

**🔹Why we need Maven**

Since the Shipping Service is written in Java, we use Maven to build and package the application. Maven also handles required libraries and simplifies the build process.

The required versions should be confirmed with the developer. In this case, Java 1.8 or higher and Maven 3.5 or higher are required.

**Install Maven, which is required to build and package the Shipping Service application**

```
dnf install maven -y
```

**Configure the application**

The application is developed by our team and is not available as an RPM package like other software. Therefore, we need to configure and set up everything manually.

As covered in Linux basics, applications should not run as the root user and must use a non-root user for better security.

**Create a dedicated user for running the application**

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

**Download the application source code into the application directory**

```
curl -L -o /tmp/shipping.zip https://roboshop-artifacts.s3.amazonaws.com/shipping-v3.zip 
cd /app 
unzip /tmp/shipping.zip
```

Every application developed by the team uses common software libraries. These dependencies are defined in the application configuration file.

**Let’s install the required dependencies and build the application**

```
cd /app 
mvn clean package 
mv target/shipping-1.0.jar shipping.jar
```

**We need to create a systemd service so systemctl can manage it**

As discussed in Linux basics, using systemctl to manage services has many advantages. So, we are following this approach, which is also the standard way in Linux operating systems.

**Set up a systemd service for the Shipping Service**

```
vi /etc/systemd/system/shipping.service
```

```
[Unit]
Description=Shipping Service

[Service]
User=roboshop
// highlight-start
Environment=CART_ENDPOINT=<CART-SERVER-IPADDRESS>:8080
Environment=DB_HOST=<MYSQL-SERVER-IPADDRESS>
// highlight-end
ExecStart=/bin/java -jar /app/shipping.jar
SyslogIdentifier=shipping

[Install]
WantedBy=multi-user.target
```

**Reload systemd to load the new service configuration**

```
systemctl daemon-reload
```

**Enable & Start the Shipping Service using systemctl**

```
systemctl enable shipping
```

```
systemctl start shipping
systemctl status shipping
```

To make the application fully functional, we need to load the schema into the database.

Schemas are usually provided by the development team as part of the application code. We need to load these schemas into the database, and for that, we must install the MySQL client.

**We can install it using the following command**

```
dnf install mysql -y
```

**Load the Schema. A database schema defines the structure of the database, including tables and their design required for the application.**

```
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -proboshop@1 < /app/db/schema.sql
```

**We need to create an application user in MySQL because it requires password authentication. This user will be used by the Shipping Service to connect to the database.**

```
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pRoboShop@1 < /app/db/app-user.sql
```

**Load master data, which includes information about all countries, their cities, and the distances between those cities.**

```
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pRoboShop@1 < /app/db/master-data.sql
```

**This service needs to be restarted because it depends on the database schema. After loading the schema, the service will work correctly, so we restart it to apply the changes.**

```
systemctl restart shipping
```




