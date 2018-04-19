---
title: Add User into sudoers
date: 2018-04-19 14:51:44
categories: "Linux"
tags:
    - Linux
keywords: Linux
---

> 通过 `sudo` 以 root 用户身份执行命令

切换到 root 用户，执行 visudo 打开配置文件

```
$ su root
# visudo
```

找到配置文件中的 `root    ALL=(ALL)       ALL` 在其后新增一行

```
username    ALL=(ALL)       ALL
```

保存后，对应用户便可以使用 sudo 以 root 用户的身份执行命令

此时，执行命令需要输入当前用户的密码，若要免密，修改配置如下即可

```
username  ALL=(ALL)       NOPASSWD:ALL
```


