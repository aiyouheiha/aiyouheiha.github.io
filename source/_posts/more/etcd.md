---
title: etcd
date: 2018-08-14 19:01:00
categories: "更多"
tags:
    - etcd
keywords: etcd
---

- [coreos/etcd](https://github.com/coreos/etcd)

> A distributed, reliable key-value store for the most critical data of a distributed system.

<!-- more -->

## Install

- https://github.com/coreos/etcd/releases

### macOS

```
$ brew install etcd
$ etcd
$ export ETCDCTL_API=3
$ etcdctl -h
```

## Lease

> See `etcdctl lease -h`

### 创建租约/租约列表

```
$ etcdctl lease list
found 0 leases
$ etcdctl lease grant 100
lease 694d65380fd65b04 granted with TTL(100s)
$ etcdctl lease list     
found 1 leases
694d65380fd65b04
```

### 租约剩余时间

```
$ etcdctl lease timetolive 694d65380fd65b04
lease 694d65380fd65b04 granted with TTL(100s), remaining(73s)
$ etcdctl lease timetolive 694d65380fd65b04
lease 694d65380fd65b04 granted with TTL(100s), remaining(71s)
```

### 设置 Key Value 并关联租约

```
$ etcdctl put test 123 --lease=694d65380fd65b04
OK
$ etcdctl get test                             
test
123
```

租约过期，对应的 Key Value 也会被删除

```
$ etcdctl lease timetolive 694d65380fd65b04
lease 694d65380fd65b04 already expired
$ etcdctl get test
```

- **PS** `put` 操作并不会更新租约时间

### 删除租约

```
$ etcdctl put test 456 --lease=694d65380fd65b0b
OK
$ etcdctl get test
test
456
$ etcdctl lease revoke 694d65380fd65b0b
lease 694d65380fd65b0b revoked
$ etcdctl get test
```

租约删除后，关联 key 上的数据亦被删除

### Keep Alive

定期的去更新租约

```
$ etcdctl lease grant 10                   
lease 694d65380fd65b14 granted with TTL(10s)
$ etcdctl lease keep-alive 694d65380fd65b14
lease 694d65380fd65b14 keepalived with TTL(10)
lease 694d65380fd65b14 keepalived with TTL(10)
lease 694d65380fd65b14 keepalived with TTL(10)

...

```

