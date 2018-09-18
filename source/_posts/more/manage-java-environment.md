---
title: Manage your Java environment
date: 2018-09-18 15:10:08
categories: "Java"
tags:
    - jEnv
    - Java
keywords: jEnv, Java
---

> [jEnv](http://www.jenv.be) - Manage your Java environment

<!-- more -->

## Installation

Linux / OS X

```
$ git clone https://github.com/gcuisinier/jenv.git ~/.jenv
```

Mac OS X via Homebrew

```
$ brew install jenv
```

## Installation - Enable

Bash

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile
```

Zsh

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(jenv init -)"' >> ~/.zshrc
```

## Configure

```
$ jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home
oracle64-1.8.0.152 added
1.8.0.152 added
1.8 added
$ jenv add /Library/Java/JavaVirtualMachines/jdk-10.0.2.jdk/Contents/Home
oracle64-10.0.2 added
10.0.2 added
10.0 added
```

## And Use !

List managed JDKs

```
$ jenv versions
* system (set by /Users/sinfonia/.jenv/version)
  1.8
  1.8.0.152
  10.0
  10.0.2
  oracle64-1.8.0.152
  oracle64-10.0.2
```

Configure global version

```
$ jenv global oracle64-1.8.0.152
```

e.g.

```
$ java -version
java version "10.0.2" 2018-07-17
Java(TM) SE Runtime Environment 18.3 (build 10.0.2+13)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.2+13, mixed mode)
$ jenv global oracle64-1.8.0.152
$ java -version
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```

Configure local version (per directory)

```
$ jenv local oracle64-1.8.0.152
```

Configure shell instance version

```
$ jenv shell oracle64-1.8.0.152
```

## Without JEnv ?

**以下内容为引用，暂未实际试用，详见参考链接**

1. 在用户目录下的 bash 配置文件 .bashrc 中配置 JAVA_HOME 的路径

```
$ export JAVA_6_HOME=/System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
$ export JAVA_7_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0.jdk/Contents/Home
$ export JAVA_8_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0.jdk/Contents/Home
$ export JAVA_HOME=$JAVA_7_HOME
```

2. 创建 alias 命令动态切换 JAVA_HOME 的配置

```
$ alias jdk8='export JAVA_HOME=$JAVA_8_HOME'
$ alias jdk7='export JAVA_HOME=$JAVA_7_HOME'
$ alias jdk6='export JAVA_HOME=$JAVA_6_HOME'
```

## 参考链接

- [Mac下同时安装多个版本的JDK](https://gist.github.com/ameizi/2d9908e8b6df9078904a#mac下同时安装多个版本的jdk)


