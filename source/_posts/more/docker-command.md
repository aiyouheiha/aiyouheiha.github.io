---
title: Docker 命令
date: 2018-07-13 10:29:08
categories: "Docker"
tags:
    - Docker
keywords: Docker
---

> Use `command --help` is helpful.

<!-- more -->

---

## 获取容器 IP

```
# docker inspect 'container_name' |  grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

---

## 强制批量删除 none 的镜像

> [Docker 强制批量删除 none 的镜像](https://blog.csdn.net/xl_lx/article/details/81565583)

```
$ docker images
REPOSITORY                                   TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
<none>                                       <none>              763e6d509697        2 minutes ago       1.123 GB
<none>                                       <none>              008ec8098c8d        21 minutes ago      471 MB

$ docker images|grep none|awk '{print $3 }'|xargs docker rmi
```

---

## 保存（导出）镜像

```
$ docker images
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
docker.elastic.co/logstash/logstash             6.4.0               13b7a09abaf8        3 weeks ago         670 MB
```

通过如下命令进行打包，可以同时打包镜像相关的元数据信息（`REPOSITORY`, `TAG` 等）

```
$ docker save -o logstash.6.4.0.tar docker.elastic.co/logstash/logstash:6.4.0
```

- **Note**
    - 通过以下命令也可完成打包，但 **无法** 保存元数据信息
        - `docker save 13b7a09abaf8 > logstash.6.4.0.tar`
    - 打包后的文件太大，不方便传输？可以试着通过 `split` 命令分割
        - `split -b 100M -a 1 -d logstash.6.4.0.tar logstash.6.4.0.tar.`
        - {% post_link more/linux-file-split-and-cat "Linux 下文件分割与合并 - split and cat" %}

---

## 导入镜像

```
$ docker load --input logstash.6.4.0.tar
```

或者

```
$ docker load < logstash.6.4.0.tar
```

---

## Basic

```
$ docker --version
Docker version 17.12.0-ce, build c97c6d6
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

- `docker version`
    - 显示 Docker 版本信息

- `docker search redis`
    - 从 Docker Hub 查找镜像

- `docker pull redis` or `docker pull redis:latest` or `docker pull redis:other-version`
    - 从镜像仓库中拉取或者更新指定镜像

## `docker images`

```
$ docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs
```

## `docker image`

```
$ docker image --help 

Usage:  docker image COMMAND

Manage images

Options:


Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
```

## `docker ps`

```
$ docker ps --help

Usage:  docker ps [OPTIONS]

List containers

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display numeric IDs
  -s, --size            Display total file sizes
```

## `docker rm`

```
$ docker rm --help

Usage:  docker rm [OPTIONS] CONTAINER [CONTAINER...]

Remove one or more containers

Options:
  -f, --force     Force the removal of a running container (uses SIGKILL)
  -l, --link      Remove the specified link
  -v, --volumes   Remove the volumes associated with the container
```

## Reference Linking

- [Docker 给运行中的容器设置端口映射的方法](https://www.centos.bz/2017/11/docker-给运行中的容器设置端口映射的方法/)


