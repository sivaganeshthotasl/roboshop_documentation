### 01-Frontend ###



**The frontend is the service in RoboShop to serve the web content over Nginx. This will have the webframe for the web application**.

👉 **This is static content, and we need a web server to serve it.**

👉 **The developer has chosen Nginx as the web server, so we will install the Nginx web server.**

**You can list modules by using :-**

```bash
dnf module list nginx
```

**Install Nginx -**
```bash
dnf module disable nginx
dnf module enable nginx:1.24 -y
dnf install nginx -y
```

**Start & Enable Nginx Service :-**
```bash
systemctl enable nginx
systemctl start nginx
```

**Try to access the service in a browser and make sure you see the default content**.

**Remove the default content that web server is serving :-**
```bash
rm -rf /usr/share/nginx/html/*
```

**Download the frontend content :-**
```bash
curl -o /tmp/frontend.zip https://roboshop-artifacts.s3.amazonaws.com/frontend-v3.zip
```

**Extract the frontend content :-**
```bash
cd /usr/share/nginx/html
unzip /tmp/frontend.zip
```

Try to access the nginx service once more over the browser and ensure you get roboshop content.

**Create Nginx Reverse Proxy Configuration to reach backend services :-**
```bash
vim /etc/nginx/nginx.conf
```

**Add the following content :-**
```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }

        location /images/ {
          expires 5s;
          root   /usr/share/nginx/html;
          try_files $uri /images/placeholder.jpg;
        }
        location /api/catalogue/ { proxy_pass http://localhost:8080/; }
        location /api/user/ { proxy_pass http://localhost:8080/; }
        location /api/cart/ { proxy_pass http://localhost:8080/; }
        location /api/shipping/ { proxy_pass http://localhost:8080/; }
        location /api/payment/ { proxy_pass http://localhost:8080/; }

        location /health {
          stub_status on;
          access_log off;
        }

    }
}
```
NOTE: Ensure you replace the localhost with the actual ip address of those component servers. Word localhost is just used to avoid the failures on the Nginx Server.

**Restart Nginx Service to load the changes of the configuration :-**

```bash
systemctl restart nginx
```









