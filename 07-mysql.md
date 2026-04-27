**🐬 MySQL**

MySQL is an open-source relational database used to store and manage application data. It organizes data in tables (rows and columns) and allows us to query data using SQL.

Developer has chosen the database MySQL. Hence, we are trying to install it up and configure it.

We need to check with the developer for the required database version. Here, the developer has provided MySQL 8.x as the required version.

**Install the MySQL server required for the application.**

```
dnf install mysql-server -y
```

**Enable & Start the MySQL service using systemctl**

```
systemctl enable mysqld
```

```
systemctl start mysqld
systemctl status mysqld
```

Next, we need to change the default root password to start using the database service. You can set the password as roboshop@1 or choose any secure password.

```
mysql_secure_installation --set-root-pass roboshop@1
```

