---
title: Mac Install Jenkins
date: 2018-07-13 10:29:08
categories: "Jenkins"
tags:
    - Jenkins
keywords: Jenkins
---

## Install or Upgrade

```
$ brew install jenkins
Error: jenkins 2.101 is already installed
To upgrade to 2.131, run `brew upgrade jenkins`

$ brew upgrade jenkins
```

## Uninstall or Remove All

```
$ brew uninstall jenkins
Uninstalling /usr/local/Cellar/jenkins/2.131... (7 files, 75MB)
jenkins 2.101 1 is still installed.
Remove all versions with `brew uninstall --force jenkins`.

$ brew uninstall --force jenkins
```

## Start

```
$ brew services start jenkins
```

浏览器访问 `localhost:8080`

## Unlock Jenkins

按照提示，找到 `initialAdminPassword` 输入并继续。

## 用户管理

- [Jenkins 配置记录 -- 添加用户权限](https://www.cnblogs.com/kevingrace/p/6019707.html)




