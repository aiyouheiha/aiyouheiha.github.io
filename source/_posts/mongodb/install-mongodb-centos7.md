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

**注** 下面的操作是直接在 root 权限下进行的！关于如何使用非 root 权限，以后也许会追加上吧……

……有需求的时候 (o゜▽゜)o☆

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

```
ERROR: child process failed, exited with error number 1
```

上面错误，可能是 mongod.conf 中配置的路径不一致问题


```
ERROR: child process failed, exited with error number 100
```

上面错误，可能是没有正常关闭导致的，那么可以删除 mongod.lock 文件，再行启动，可能会用到

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


## 参考链接

- [Install MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-mongodb-community-edition)
