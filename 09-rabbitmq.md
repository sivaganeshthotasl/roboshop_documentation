**🐰 RabbitMQ**

RabbitMQ is a message queue that helps application components communicate without directly depending on each other.

The database software version will be provided by the developer. In this case, the developer has shared the version as RabbitMQ 3.x.

**👉 Create the RabbitMQ repo file using**

```
vim /etc/yum.repos.d/rabbitmq.repo
```

```
[modern-erlang]
name=modern-erlang-el9
baseurl=https://yum1.novemberain.com/erlang/el/9/$basearch
        https://yum2.novemberain.com/erlang/el/9/$basearch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/$basearch
enabled=1
gpgcheck=0

[modern-erlang-noarch]
name=modern-erlang-el9-noarch
baseurl=https://yum1.novemberain.com/erlang/el/9/noarch
        https://yum2.novemberain.com/erlang/el/9/noarch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/noarch
enabled=1
gpgcheck=0

[modern-erlang-source]
name=modern-erlang-el9-source
baseurl=https://yum1.novemberain.com/erlang/el/9/SRPMS
        https://yum2.novemberain.com/erlang/el/9/SRPMS
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/SRPMS
enabled=1
gpgcheck=0

[rabbitmq-el9]
name=rabbitmq-el9
baseurl=https://yum2.novemberain.com/rabbitmq/el/9/$basearch
        https://yum1.novemberain.com/rabbitmq/el/9/$basearch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/9/$basearch
enabled=1
gpgcheck=0

[rabbitmq-el9-noarch]
name=rabbitmq-el9-noarch
baseurl=https://yum2.novemberain.com/rabbitmq/el/9/noarch
        https://yum1.novemberain.com/rabbitmq/el/9/noarch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/9/noarch
enabled=1
gpgcheck=0
```

**👉 Install RabbitMQ using**

```
dnf install rabbitmq-server -y
```

**👉 Enable and Start the RabbitMQ service**

```
systemctl enable rabbitmq-server
systemctl start rabbitmq-server
```

**RabbitMQ provides a default login (guest/guest), but it cannot be used by the application. So, we create a new user.**

**👉 Create a new user in RabbitMQ**
```
rabbitmqctl add_user roboshop roboshop123
```
Create a new user in RabbitMQ

--> roboshop → username
--> roboshop123 → password

👉 This user will be used by your application to connect to RabbitMQ

```
rabbitmqctl set_permissions -p / roboshop ".*" ".*" ".*"
```

Give full access permission to the user

Break it down (very easy)

```-p /```     → default environment (called vhost)

```roboshop``` → user name

```"."*```     →  means allow everything

👉 Three permissions:

1. Configure → create/change queues

2. Write → send messages

3. Read → receive messages

In simple words:

**We create a user and give it full permission to use RabbitMQ.**







