**01- Frontend**

**The frontend is the service in RoboShop to serve the web content over Nginx. This will have the webframe for the web application**.

👉 **This is static content, and we need a web server to serve it.**
👉 **The developer has chosen Nginx as the web server, so we will install the Nginx web server.**

**You can list modules by using**

```dnf module list nginx```

**Install Nginx:-**

```
dnf module disable nginx
dnf module enable nginx:1.24 -y
dnf install nginx -y```








