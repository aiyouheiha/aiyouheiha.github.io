---
title: rpm
date: 2018-04-19 16:48:26
categories: "Linux"
tags:
    - Linux
keywords: Linux
---

## 安装

> rpm -ivh <xxx.rpm>

```
$ sudo rpm -ivh jdk-8u162-linux-x64.rpm
准备中...                          ################################# [100%]
正在升级/安装...
   1:jdk1.8-2000:1.8.0_162-fcs        ################################# [100%]
Unpacking JAR files...
	tools.jar...
	plugin.jar...
	javaws.jar...
	deploy.jar...
	rt.jar...
	jsse.jar...
	charsets.jar...
	localedata.jar...
$ java -version
java version "1.8.0_162"
Java(TM) SE Runtime Environment (build 1.8.0_162-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.162-b12, mixed mode)
```




