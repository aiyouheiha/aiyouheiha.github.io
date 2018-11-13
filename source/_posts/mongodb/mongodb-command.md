---
title: MongoDB 命令
date: 2017-11-06 15:59:17
categories: "MongoDB"
tags:
    - MongoDB
keywords: MongoDB
---

这里会简单介绍 MongoDB 操作命令（持续更新……）

<!-- more -->

## 远程登录

```
$ mongo x.x.x.x:27017/target
```

```
$ mongo x.x.x.x:27017/target -u username -p
MongoDB shell version v3.6.1
Enter password: 
```

## 版本号

```
db.version()
```

## 身份认证

```
> db.auth("admin", "123455")
Error: Authentication failed.
0
> db.auth("admin", "123456")
1
```

## 数据库列表

```
show dbs
```

## 当前位置

```
db
```

## 查看用户

```
show users
db.system.users.find()
```

## 创建用户

```
db.createUser({user:"root",pwd:"123456",roles:["root"]})
db.createUser(
    {
        user: "admin", 
        customData: {
            description: "superuser"
        }, 
        pwd: "123456", 
        roles: [
            {
                role: "userAdminAnyDatabase", 
                db: "admin"
            }
        ]
    }
)
```

## 查询文档

```
db.getCollection('test').find({})
db.getCollection('test').find(
    {
        "id": "xxx"
    }
)
```

## 插入文档

```
db.getCollection('test').insert(
    {
        "id": "yyy", 
        "isTest": false, 
        "test": {
            "id": 1
        }
    }
)
```

## 更新文档

文档中对象结构如下

```
{
    "_id" : ObjectId("59fc4cb94422cb484447463f"),
    "id" : "yyy",
    "isTest" : false,
    "test" : {
        "id" : 1.0
    }
}
```

更新

```
db.getCollection('test').update(
    {
        "id": "yyy"
    }, 
    {
        $set: {
            "isTest": true
        }
    }
)
```

更新内层属性

```
db.getCollection('test').update(
    {
        "id": "yyy"
    }, 
    {
        $set: {
            "test.id": 3
        }
    }
)
```

## 删除文档

```
db.getCollection('test').remove({"id": "yyy"})
```

