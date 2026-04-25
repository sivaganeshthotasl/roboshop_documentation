**🔹Redis Overview**

Redis (Remote Dictionary Server) is an open-source, in-memory data store. It is commonly used as a database and cache.

Redis is very fast because it stores data in RAM (memory) instead of disk.

**👉Key Features**

--> Uses a key–value data model  --> Supports advanced data types like lists, sets, and hashes  --> Provides high performance due to in-memory storage

**👉Why Redis is Used**

--> To improve application performance  --> To reduce load on databases  --> To store frequently accessed data (caching)

**👉WVersion Selection**

**The Redis version should be selected based on application requirements. We need to confirm the required version with the developer before installation.**

**👉WInstallation Note**

By default, Redis 6 is available in the system repositories. If the project requires Redis 7, we need to:

**👉WDisable & Enable redis:7**

```
dnf module disable redis -y
```

```
dnf module enable redis:7 -y
```

**👉Install Redis**

```
dnf install redis -y
```

By default, Redis listens only on localhost (127.0.0.1). This means the Redis service can be accessed only by applications running on the same server.

In our setup, Redis must be accessed from another server (for example, an application server).

To allow this, we need to update the Redis configuration.

🔹Update listen address from 127.0.0.1 to 0.0.0.0 in /etc/redis/redis.conf

🔹Update protected-mode from yes to no in /etc/redis/redis.conf

```
vi /etc/redis/redis.conf
```

1️⃣ Update bind address Change:

```bind 127.0.0.1 to bind 0.0.0.0```
This allows Redis to accept connections from all network interfaces.

2️⃣ Disable protected mode

```protected-mode yes to protected-mode no```

**👉Start & Enable Redis Service**

```
systemctl enable redis
```

```
systemctl start redis
```

**🔹Summary**

By default, Redis allows only local connections. To enable access from other servers, update the bind address and disable protected mode in the configuration file.




