---
title: Git 命令
date: 2017-12-11 10:31:12
categories: "Git"
tags:
    - Git
keywords: Git
---

这里会简单介绍 Git 操作命令（持续更新……）

<!-- more -->

# tag

```
git tag [-a | -s | -u <key-id>] [-f] [-m <msg> | -F <file>] <tagname> [<head>]
git tag -d <tagname>...
git tag -l [-n[<num>]] [--contains <commit>] [--no-contains <commit>] [--points-at <object>] [--format=<format>] [--[no-]merged [<commit>]] [<pattern>...]
git tag -v [--format=<format>] <tagname>...

    -l, --list            list tag names
    -n[<n>]               print <n> lines of each tag message
    -d, --delete          delete tags
    -v, --verify          verify tags

Tag creation options
    -a, --annotate        annotated tag, needs a message
    -m, --message <message>
                          tag message
    -F, --file <file>     read message from file
    -s, --sign            annotated and GPG-signed tag
    --cleanup <mode>      how to strip spaces and #comments from message
    -u, --local-user <key-id>
                          use another key to sign the tag
    -f, --force           replace the tag if exists
    --create-reflog       create a reflog

Tag listing options
    --column[=<style>]    show tag list in columns
    --contains <commit>   print only tags that contain the commit
    --no-contains <commit>
                          print only tags that don't contain the commit
    --merged <commit>     print only tags that are merged
    --no-merged <commit>  print only tags that are not merged
    --sort <key>          field name to sort on
    --points-at <object>  print only tags of the object
    --format <format>     format to use for the output
    -i, --ignore-case     sorting and filtering are case insensitive
```

### 新建标签

```
$ git tag -a v0.0.1 -m "version 0.0.1"
$ git tag v0.0.2-light
```

### 标签列表

```
$ git tag
v0.0.1
v0.0.2-light

$ git tag -l
v0.0.1
v0.0.2-light

$ git tag -n1
v0.0.1          version 0.0.1
v0.0.2-light    update xxx
```

### 查看标签详情

```
$ git show v0.0.2-light
```

### 切换到指定标签

```
$ git checkout v0.0.1
```

### 删除标签

```
$ git tag -d v0.0.1
Deleted tag 'v0.0.1' (was bce8bc1)
```

### 为指定 commit 添加标签

```
$ git tag -a v0.0.1-x 77aba8
$ git tag -n1
v0.0.1-x        version 0.0.1-x
v0.0.2-light    update xxx
```

### 提交标签到远程

通常 `git push` 不会将标签提交，需显式操作：

提交 v0.0.1 标签

```
$ git push origin v0.0.1
```

提交本地所有标签

```
$ git push origin --tags
```
