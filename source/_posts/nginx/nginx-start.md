---
title: nginx 初识
date: 2018-05-04 10:14:59
categories: "nginx"
tags:
    - nginx
keywords: nginx
---

说明：

1. 作为入门，初始操作均在 root 用户下进行，也即未考虑安全问题。

---

## nginx 安装与使用

### 系统信息

```
# uname -a
Linux VM_0_4_centos 3.10.0-514.26.2.el7.x86_64 #1 SMP Tue Jul 4 15:04:05 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

### 安装

```
# yum install nginx
# nginx -v
nginx version: nginx/1.12.2
# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 基本控制命令

```
nginx              # 启动 nginx

nginx -v           # show version and exit
nginx -t           # test configuration and exit

nginx -s signal    # send signal to a master process: stop, quit, reopen, reload
nginx -s stop      # 停止 nginx
nginx -s reopen    # 重启 nginx
nginx -s reload    # 重新载入配置文件
```

### 配置文件

```
# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

其中 `/etc/nginx/nginx.conf` 即为 nginx 的配置文件。

### 以默认配置启动并访问

- 使用命令 `nginx` 启动 nginx
- 查看配置文件 `/etc/nginx/nginx.conf`
    - 其中 `root /usr/share/nginx/html;` 该行指定了 nginx 默认的服务根目录。

通过 `curl localhost` 可以访问到默认工作目录 `/usr/share/nginx/html` 下 index.html 中的内容。

### 自定义根目录

找到文件中的 `root /usr/share/nginx/html;` 修改路径为自定义的路径，例如

```
# root /usr/share/nginx/html;
root /home/test/static;
```

保存并关闭文件，执行 `nginx -s reload` 重新加载配置文件，并再次访问

```
# curl localhost
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.12.2</center>
</body>
</html>
```

返回 403 Forbidden 可能是因为以下原因：

1. 根目录下没有 index.html 文件（或者其他通过 index 配置项指定的入口文件，不配置默认为 index.html）
2. 与 Linux 目录访问权限相关。

即使通过 root 用户启动 nginx，其工作进程依然是配置文件中 `user nginx;` 所指定的用户。

通过 `ps aux | grep nginx` 查看 nginx 进程信息，可以看到 `nginx: worker process` 对应用户为 `nginx`。

```
# ps aux | grep nginx
root      5356  0.0  0.2 124236  5392 ?        Ss   10:26   0:00 nginx: master process nginx
nginx     6145  0.0  0.2 126324  4444 ?        S    10:41   0:00 nginx: worker process
```

而自定义路径 `/home/test/static` 中的 test 目录，是用户 test 的用户目录，其访问权限如下，

```
drwx------ 3 root  root  4096 5月   4 10:42 test
```

其他用户（当前指 nginx 用户）是不具备该目录的访问权限的，
为该目录配置其他用户的 **执行权限（执行 index.html）** 即可顺利访问。

```
# chmod +001 test/
# ll
drwx-----x 3 test  test  4096 5月   4 11:30 test
```





