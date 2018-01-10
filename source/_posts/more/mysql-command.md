---
title: MySQL 命令
date: 2018-01-10 10:33:01
categories: "更多"
tags:
    - MySQL
keywords: MySQL
---

这里会简单介绍 MySQL 操作命令（持续更新……）

<!-- more -->

## 库操作

```mysql
show databases;
create database db_name;
use db_name;
drop database db_name;
```

## 表操作

### 列出表

```mysql
show tables;
```

### 新建表

```mysql
CREATE TABLE `tb_name` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Primary Key',
  `name` varchar(30) NOT NULL COMMENT 'Name',
  `description` varchar(30) DEFAULT NULL COMMENT 'Description',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 表结构

```mysql
describe tb_name;
```

## 用户操作

### 新建用户

```mysql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

e.g.

```
mysql> CREATE USER 'test'@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.02 sec)

mysql> select Host, User from mysql.user;
+-----------+---------------+
| Host      | User          |
+-----------+---------------+
| %         | test          |
| localhost | mysql.session |
| localhost | mysql.sys     |
| localhost | root          |
+-----------+---------------+
4 rows in set (0.00 sec)
```

### 用户授权

```mysql
GRANT INSERT, DELETE, UPDATE, SELECT ON db_name.tb_name TO 'username'@'host';
GRANT INSERT, DELETE, UPDATE, SELECT ON db_name.* TO 'username'@'host';
GRANT ALL ON db_name.* TO 'username'@'host';
```

### 取消授权

```mysql
REVOKE ALL ON db_name.* FROM 'username'@'host';
REVOKE INSERT, DELETE, UPDATE, SELECT ON db_name.tb_name FROM 'username'@'host';
```


## 参考链接

- [MySQL 用户表中多个 Host 时的匹配规则](http://blog.csdn.net/liuxiao723846/article/details/49583827)
- [MySQL 创建用户与授权](https://www.jianshu.com/p/d7b9c468f20d)

