---
title: Docker 命令
date: 2018-07-13 10:29:08
categories: "Docker"
tags:
    - Docker
keywords: Docker
---

```
$ docker version
Client:
 Version: 17.12.0-ce
 API version: 1.35
 Go version:  go1.9.2
 Git commit:  c97c6d6
 Built: Wed Dec 27 20:03:51 2017
 OS/Arch: darwin/amd64

Server:
 Engine:
  Version:  17.12.0-ce
  API version:  1.35 (minimum version 1.12)
  Go version: go1.9.2
  Git commit: c97c6d6
  Built:  Wed Dec 27 20:12:29 2017
  OS/Arch:  linux/amd64
  Experimental: true
```


```
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
itzg/minecraft-server   latest              a5b47db7ac2f        3 months ago        304MB
emqttd-docker-v2.3.5    latest              1e0539f75a28        4 months ago        77.8MB
```

```
$ docker search openjdk
NAME                                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
openjdk                             OpenJDK is an open-source implementation of …   1065                [OK]                
azul/zulu-openjdk                   Zulu is a fully tested, compatibility verifi…   70                                      [OK]
oracle/openjdk                      Docker images containing OpenJDK Oracle Linux   44                                      [OK]
```
