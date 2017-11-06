---
title: MongoDB 安装及使用 - CentOS7
date: 2017-11-03 11:17:08
categories: "MongoDB"
tags:
    - MongoDB
    - CentOS
keywords: CentOS, MongoDB
---

这里会简单介绍如何在 CentOS7 上安装及使用 MongoDB，但是，不要指望会有多全面。

<!-- more -->

## 配置包管理系统

```
$ su root
$ cd /etc/yum.repos.d
$ sudo touch mongodb-org-3.4.repo
$ sudo vim mongodb-org-3.4.repo
```

文件内容如下

```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

## 安装

```
$ sudo yum install -y mongodb-org
```

## 配置工作目录 

编辑配置文件

```
$ sudo vim /etc/mongod.conf
```

默认配置文件中有下面两项配置

```
storage:
  dbPath: /var/lib/mongo
systemLog:
  path: /var/log/mongodb/mongod.log
```

这两项指定了数据和日志的存储路径，使用默认是可行的，请根据自身需求决定。

这里将配置修改如下

```
storage:
  dbPath: /home/mongo/data/db
systemLog:
  path: /home/mongo/log/mongod.log
```

同时创建路径，操作如下

```
$ cd /usr/local/
$ sudo mkdir mongodb
$ cd mongodb/
$ sudo mkdir -p data/db
$ sudo mkdir log
$ cd log
$ touch mongod.log
```

## 启动服务

**注** 下面的操作是直接在 root 权限下进行的！~~关于如何使用非 root 权限，以后也许会追加上吧……~~

~~……有需求的时候 (o゜▽゜)o☆~~

如何使用非 root 用户启动，已经在下文添加，详见 [非 root 用户启动](#非-root-用户启动)

```
$ su root
# systemctl start mongod.service
```

如果出现启动错误，可以通过下面的方式启动

解决错误？不存在的，懒 (o゜▽゜)o☆

```
# mongod --config /etc/mongod.conf
```

关于启动方式：

- 以自定义的 mongodb 配置文件方式启动：

```
# mongod --config mongod.conf
```

- 以修复模式启动 mongodb：

```
mongod --repair -f mongod.conf
```

- 以参数式启动：

```
mongod \
--dbpath=/usr/local/mongodb/data/db \
--logpath=/usr/local/mongodb/log/mongod.log \
--fork
```

启动成功，可通过客户端登录，并简单操作检验

```
# mongo
> db,version();
3.4.6
```

## 可能出现的报错

**HINT** 仅仅是可能出现的错误原因，以供参考。最好的办法依旧是 **查看运行日志中记录的错误原因**

```
ERROR: child process failed, exited with error number 1
```

上面错误，可能是 mongod.conf 中配置的路径不一致问题


```
ERROR: child process failed, exited with error number 100
```

上面错误，可能是没有正常关闭导致的，那么可以删除 mongod.lock 文件，再行启动，可能会用到修复模式

**PS** 锁和日志文件，在 mongod.conf 的指定目录下

```
ERROR: child process failed, exited with error number 100
```

上面错误，可能是配置文件有问题，请检查配置文件


### 正确的关闭

1. When running the mongod instance in interactive mode `Ctrl - C`
2. `mongod --shutdown`
3. `kill <mongod process ID>`
    - Never use kill -9 (i.e. SIGKILL) to terminate a mongod instance.
4. Use `db.shutdownServer()` under `admin`

```
# mongo
> use admin
switched to db admin
> db.shutdownServer();
> exit
bye
```


## 一个排错的栗子

进行 MongoDB 的访问控制，需要在 bindIp 后，添加需要访问 MongoDB 的 IP 地址

修改配置如下（对于本文安装的 MongoDB 版本来说，是错误的哈）

```
net:
  bindIp: 127.0.0.1,123.123.123.123
```

关闭数据服务

```
# mongo
> use admin
switched to db admin
> db.shutdownServer();
> exit
bye
```

这是正确的关闭，但是重启服务，却出现了报错

```
# mongod --config mongod.conf 
about to fork child process, waiting until server is ready for connections.
forked process: 28080
ERROR: child process failed, exited with error number 48
```

于是，删锁，删日志的，修复模式等……没有效果

算了，自己看下日志吧

```
listen(): bind() failed Cannot assign requested address for socket: 123.123.123.123:27017
```

也许这叫……偷懒的下场？

移除配置文件中的错误配置 `123.123.123.123`，正常启动，可以启动成功。

### 正确的权限控制配置方式

IP访问控制：在 bindIp 后，添加需要访问 MongoDB 的客户端 IP 地址

首先，关闭 mongo 服务

```
# mongod --config /etc/mongod.conf --shutdown
killing process with pid: 28715
```

修改配置文件 `/etc/mongod.conf`

```
net:
  port: 27017
  bindIp:
    - 127.0.0.1  # Listen to local interface only, comment to listen on all interfaces.
    - 123.123.123.123
```

然后，重启，便可以顺利启动了。

关于之前的错误，应当是当前版本配置文件是 yaml 形式的，之前错误配置的方式无法被识别。

**如果并不是这个原因，请务必告诉我**

测试远程连接

```
mongo x.x.x.x:27017/admin
```

success

## 设置访问密码

创建 root/admin 用户

```
$ mongo
> use admin
switched to db admin
> db
admin
> show users;
> db.createUser({user:"root",pwd:"123456",roles:["root"]})
Successfully added user: { "user" : "root", "roles" : [ "root" ] }
> db.createUser({user:"admin",pwd:"123456",roles:[{role:"userAdminAnyDatabase",db:"admin"}]})
Successfully added user: {
	"user" : "admin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		}
	]
}
> show users;
{
	"_id" : "admin.admin",
	"user" : "admin",
	"db" : "admin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		}
	]
}
{
	"_id" : "admin.root",
	"user" : "root",
	"db" : "admin",
	"roles" : [
		{
			"role" : "root",
			"db" : "admin"
		}
	]
}
> db.system.users.find()
{ "_id" : "admin.root", "user" : "root", "db" : "admin", "credentials" : { "SCRAM-SHA-1" : { "iterationCount" : 10000, "salt" : "yos3QUM0HICfWQuveww6Uw==", "storedKey" : "6ws6buooQaz5Eho068qX4HagpF8=", "serverKey" : "trfuBWIqVmy/Yl8Taj2E220GxK8=" } }, "roles" : [ { "role" : "root", "db" : "admin" } ] }
{ "_id" : "admin.admin", "user" : "admin", "db" : "admin", "credentials" : { "SCRAM-SHA-1" : { "iterationCount" : 10000, "salt" : "zbIUq6Gcg5GunU5Pbd2R0A==", "storedKey" : "enwbhOQHH/8m/n/ww7lufaG32tU=", "serverKey" : "MOtEW78Qw6bfq1BoKVlW4daJHzY=" } }, "roles" : [ { "role" : "userAdminAnyDatabase", "db" : "admin" } ] }
> exit
bye
```

停止当前服务

```
$ sudo mongod --config /etc/mongod.conf --shutdown
[sudo] password for mongo: 
killing process with pid: 29076
```

**PS** 此处需要使用 `sudo` 是因为之前是通过 root 权限开启的 mongo 服务，请根据具体情况决定如何使用

**HINT** 想要使用非 root 开启服务，除了在非 root 用户下启动，还需要将之前 root 权限才能访问的，数据、日志、以及配置文件的访问权限进行修改

修改配置文件

```
$ sudo vim /etc/mongod.conf
```

增加以下内容

```
security:
  authorization: enabled
```

之后重新启动即可，可继续使用 root 用户启动，也可参考下文 [非 root 用户启动](#非-root-用户启动)，配置非 root 用户启动。

启动后，通过客户端登录。在各种命令执行时，将会提示没有权限，执行下面操作，**通过验证**，才可正常使用

```
> use admin
switched to db admin
> db.auth("admin", "123456")
1
> show dbs;
admin  0.000GB
local  0.000GB
test   0.000GB
```

需要说明的是，用户 admin 具有管理权限（无法读写），用户 root 具有最高权限，具体使用可以参考以下两个链接
- **[用户角色介绍](https://docs.mongodb.com/manual/reference/built-in-roles/)** 
- **[MongoDB用户角色配置](http://www.cnblogs.com/out-of-memory/p/6810411.html)**

**PS** 
- root/admin 用户并非是必须的，此处仅仅是以此作为演示。
- 如果有一个想要删除的用户，且没有具有 root 权限的用户，在权限验证开启状态下，是无法进行删除操作的。
- 无 root 权限用户时的，操作流程：关闭服务 -> 修改配置文件（关闭权限验证） -> 重启服务 -> 按照需求增删用户 -> 重新开启验证 -> 重启服务。

平时使用，配置一个具有读写权限的用户即可。可能需要注意的一点是，创建对应库的读写用户，最好切换到指定库，再执行添加操作。


## 非 root 用户启动

**PS** 改为使用 mongo 用户启动

修改目录/文件访问权限

```
$ sudo chown -R mongo:mongo /etc/mongod.conf 
$ ll /etc/mongod.conf 
-rw-r--r-- 1 mongo mongo 834 Nov  6 11:14 /etc/mongod.conf
$ sudo chown -R mongo:mongo /usr/local/mongodb/
```

重新启动

```
[mongo@x.x.x.x ~]$ mongod --config /etc/mongod.conf 
about to fork child process, waiting until server is ready for connections.
forked process: 26924
ERROR: child process failed, exited with error number 1
```

出错，查看日志文件（几乎又忘记去看日志这件事了……）

```
2017-11-06T11:31:00.412+0800 I CONTROL  [main] ERROR: Cannot write pid file to /var/run/mongodb/mongod.pid: Permission denied
```

修改访问权限

```
$ sudo chown -R mongo:mongo /var/run/mongodb/
```

重新启动

**PS** 在此之前，犯了经验主义错误，删除过锁文件和日志文件，而不是看日志找错误；如果启动失败，请自行查看日志，或者尝试删除锁文件和日志文件

```
$ mongod --config /etc/mongod.conf 
about to fork child process, waiting until server is ready for connections.
forked process: 27200
child process started successfully, parent exiting
```


## 参考链接

- [Install MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-mongodb-community-edition)
- [MongoDB用户角色配置](http://www.cnblogs.com/out-of-memory/p/6810411.html)
