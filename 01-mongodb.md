### 🍃 MongoDB ###

MongoDB is a NoSQL database that stores data in a flexible, JSON-like format (called documents) instead of tables.

👉 In simple words:
MongoDB stores data like key-value objects (similar to JSON) rather than rows and columns.


 **SQL vs NoSQL (MongoDB) Comparison**

 ## 🔹 SQL vs NoSQL (MongoDB)

| Feature            | SQL (Relational DB)                                  | NoSQL (MongoDB - Document DB)                          |
|------------------|------------------------------------------------------|--------------------------------------------------------|
| Data Structure    | Tables with rows and columns                         | Collections with JSON-like documents (JSON)             |
| Schema            | Rigid schema (must define columns)                   | Dynamic schema (schema-less, flexible)                  |
| Query Language    | SQL (Structured Query Language)                      | MQL (MongoDB Query Language - JavaScript style)         |
| Best Use Cases    | Banking, ERP, CRM, complex relational systems        | Real-time analytics, IoT, content management, apps      |
| Examples          | MySQL, PostgreSQL, Oracle, SQL Server                | MongoDB, CouchDB, Amazon DocumentDB                    |

### 🔹 Example Document (MongoDB)

```json
{
  "name": "Ganesh",
  "age": 25,
  "skills": ["AWS", "Linux", "DevOps"]
}
```

**Developer has chosen the database MongoDB. Hence, we are trying to install it up and configure it.**

**The version of the database software should be confirmed with the developer. The developer has specified the version as MongoDB 7.x.**

**Setup the MongoDB repo file:-**

**You can create file by using:-**

```
vim /etc/yum.repos.d/mongo.repo
```

**Add the below content to mongo.repo file**

```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
enabled=1
gpgcheck=0
```

✅ Why we are adding this?

By default, the system does not have MongoDB in its repository or may have an older version.
So, we add this configuration to tell the system:
👉 “Download MongoDB from the official source.”

**Install MongoDB**

```
dnf install mongodb-org -y
```

**Start & Enable MongoDB Service**

```
systemclt enable mongod
systemctl start mongod
```

**By default, MongoDB listens only on localhost (127.0.0.1), which means it can be accessed only from the same server.**

**Since our application is running on a different server, we need to update the MongoDB configuration to allow remote access.**

**Update listen address from 127.0.0.1 to 0.0.0.0 in /etc/mongod.conf**

```
vi /etc/mongod.conf
```

**Restart the service to make the changes effected.**

```
systemctl restart mongod
```


