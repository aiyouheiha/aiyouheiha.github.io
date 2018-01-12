---
title: Redis Cluster - Mac
date: 2018-01-11 16:24:06
categories: "更多"
tags:
    - Redis
    - Cluster
    - Mac
keywords: Redis Cluster, Mac
---

在 Mac 上搭建 Redis Cluster

<!-- more -->

## 安装

```
$ brew install redis
```

通过 Homebrew 安装，一般会安装到 `/usr/local/Cellar` 目录下，配置文件位于 `/usr/local/etc` 目录下，直接使用可通过如下命令，

```
$ brew services start redis
$ redis-cli
$ brew services stop redis
```

其中 `redis-cli` 会默认连接到 `6379` 端口上启动的服务，连接到指定端口，使用 `redis-cli -p 6379`

## 集群配置

```
$ cd /usr/local/etc
$ mkdir redis-cluster
$ cd redis-cluster
$ mkdir 6380 6381 6382
$ cp ../redis.conf 6380/redis-6380.conf
```

修改 `redis-6380.conf` 中的如下配置项

```
daemonize yes
cluster-enabled yes
cluster-config-file nodes-6380.conf
cluster-node-timeout 15000
appendonly yes
```

之前创建的另外两个目录下，也进行类似的操作，不做赘述。

之后，通过 `redis-server` 命令，按照不同配置文件启动。

- PS >>> 通过 Homebrew 安装，以上 `redis-server` 命令，应该已经添加到环境变量，可以直接使用
    - 若无法使用，可首先尝试重启终端
    - 该命令实际地址为 `/usr/local/Cellar/redis/4.0.6/bin/redis-server`

```
$ cd /usr/local/etc/redis-cluster
$ cd 6380
$ redis-server redis-6380.conf
$ cd ../6381
$ redis-server redis-6381.conf
$ cd ../6382
$ redis-server redis-6382.conf
```

配置文件中的 `cluster-config-file` 会自动生成在 `/usr/local/var/db/redis` 目录下

```
nodes-6380.conf
nodes-6381.conf
nodes-6382.conf
```

当前集群并非一个可用状态，下面登录其中一个节点，进行下一步配置

```
$ redis-cli -p 6380
127.0.0.1:6380> CLUSTER MEET 127.0.0.1 6381
OK
127.0.0.1:6380> CLUSTER MEET 127.0.0.1 6382
OK
127.0.0.1:6380> CLUSTER NODES
0592bba6effccae5a2634f1f1ed972adcdcbc4b8 127.0.0.1:6380@16380 myself,master - 0 1515663627000 1 connected
f0bf627ebf9668ebccf8e0dc8536b6151432a5f0 127.0.0.1:6382@16382 master - 0 1515663628816 0 connected
9000e666aee2a910b96ac060553700ee29405397 127.0.0.1:6381@16381 master - 0 1515663629825 2 connected
```

此时，通过 `CLUSTER INFO` 命令查看，集群状态 `cluster_state` 依旧是 `fail`。
原因是 16384 个 hash slot 还没有被分配给集群中节点，接下来需要分配 slot。

有两种方式分配 slot：
1. （未测试）通过 `CLUSTER ADDSLOTS` 命令来分配 slot，这种方式是一个个 slot 添加到指定节点，比较麻烦。
2. 直接在配置文件 `nodes-6380.conf/nodes-6381.conf/nodes-6382.conf` 中指定
    - 在每个文件中包含 `myself` 的行的末尾添加 slot 信息，例如在 `nodes-6380.conf` 文件中，改动如下

```
0592bba6effccae5a2634f1f1ed972adcdcbc4b8 127.0.0.1:6380@16380 myself,master - 0 1515663627000 1 connected 0-5000
```

对应的 `6381` 可分配 slot `5001-10000`，`6382` 可分配 `10001-16382`，之后重启服务

```
127.0.0.1:6380> CLUSTER NODES
0592bba6effccae5a2634f1f1ed972adcdcbc4b8 127.0.0.1:6380@16380 myself,master - 0 1515663627000 1 connected 0-5000
f0bf627ebf9668ebccf8e0dc8536b6151432a5f0 127.0.0.1:6382@16382 master - 0 1515663628816 0 connected 10001-16383
9000e666aee2a910b96ac060553700ee29405397 127.0.0.1:6381@16381 master - 0 1515663629825 2 connected 5001-10000
127.0.0.1:6380> CLUSTER INFO
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:3
cluster_size:3
cluster_current_epoch:2
cluster_my_epoch:1
cluster_stats_messages_ping_sent:388
cluster_stats_messages_pong_sent:105
cluster_stats_messages_sent:493
cluster_stats_messages_ping_received:105
cluster_stats_messages_pong_received:106
cluster_stats_messages_received:211
```

但是，测试使用可能会出现以下错误

```
127.0.0.1:6380> get test
(error) MOVED 6918 127.0.0.1:6381
```

根据错误提示，需要在 `6381` 端口的 Redis 节点上进行对应的操作，而通过 `-c` 参数登录，便可以避免这个错误

```
$ redis-cli -p 6380 -c
127.0.0.1:6380> set test "Test Message"
-> Redirected to slot [6918] located at 127.0.0.1:6381
OK
127.0.0.1:6381> get test
"Test Message"
```

## 密码认证

**说在前面** >>> 关于密码认证的配置，**并非** 最终结论，应该有更好的方式配置，但因无特别需求，未深入了解

```
127.0.0.1:6381> CONFIG SET masterauth 123456
OK
127.0.0.1:6381> CONFIG SET requirepass 123456
OK
127.0.0.1:6381> CONFIG REWRITE
(error) NOAUTH Authentication required.
```

此时，密码认证已经生效，但是并未写入配置文件，重启将会失效。
但是因为已经开启了认证 `CONFIG REWRITE` 无法执行，所以需要通过 `-a` 可选项输入密码，重新登录

```
$ redis-cli -p 6381 -c -a 123456
127.0.0.1:6381> CONFIG REWRITE
OK
```

之后可以发现 `6381` 对应的配置文件已经被重写（`6380` 与 `6382` 不会）

```
$ cd /usr/local/etc/redis-cluster/6381
$ grep -r 'masterauth' redis-6381.conf
redis-6381.conf:# masterauth <master-password>
redis-6381.conf:masterauth "123456"
$ grep -r 'requirepass' redis-6381.conf 
redis-6381.conf:# If the master is password protected (using the "requirepass" configuration
redis-6381.conf:# requirepass foobared
redis-6381.conf:requirepass "123456"
```

重启服务，会发现验证也仅在 `6381` 节点上有效，因此
- 如果想要对部分 slot 设置密码，在对应节点上进行配置（或者直接修改配置文件）
- 全部设置密码，则需要在每个节点上进行操作（直接修改配置文件会更方便些）

## 疑问

启动 6380 端口的服务 16380 端口也会被这个进程占用，其他两个节点同理。通过 `CLUSTER NODES` 命令可以发现，

```
127.0.0.1:6380@16380
127.0.0.1:6381@16381
127.0.0.1:6382@16382
```

## 参考链接

- [Redis 集群的安装和配置 - Mac](http://blog.51cto.com/libankling/1832255)
- [Redis 学习笔记(五) 基于 Redis 3.0 的集群](http://blog.csdn.net/a67474506/article/details/50435845)
- [Redis 集群设置密码](http://blog.csdn.net/daiyudong2020/article/details/51674169)



