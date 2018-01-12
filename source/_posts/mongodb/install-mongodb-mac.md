---
title: MongoDB 安装及使用 - Mac
date: 2018-01-09 15:56:10
categories: "MongoDB"
tags:
    - MongoDB
    - Mac
keywords: MongoDB, Mac
---

在 Mac 上安装及使用 MongoDB

<!-- more -->

## 安装/启动/停止/卸载

```
$ brew install mongodb
$ brew services start mongodb
$ brew services stop mongodb
$ brew uninstal mongodb
```

## 默认配置

- 文件路径 `/usr/local/etc/mongod.conf`

```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1
```

## To be continue ...



