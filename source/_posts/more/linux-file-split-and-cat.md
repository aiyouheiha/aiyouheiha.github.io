---
title: Linux 下文件分割与合并 - split and cat
date: 2018-09-11 10:00:18
categories: "更多"
tags:
    - Linux
keywords: Linux
---

```
split -- split a file into pieces
  cat -- concatenate files and print on the standard output
```

- 过大文件传输时，可以将其分割，以减少传输失败造成的影响

<!-- more -->

## Intro

```
$ man split
NAME
     split -- split a file into pieces

$ man cat
NAME
       cat - concatenate files and print on the standard output

$ split --help
$ cat --help
```

具体细节可通过上述命令查看，下面仅列举几个小栗子🌰做下说明。

## split

**分割前**

```
$ ls      
logstash.6.4.0.tar
```

**分割**

```
$ split -b 60M -d -a 2 logstash.6.4.0.tar logstash.6.4.0.tar.
```

参数说明

```
-b 60M    put SIZE bytes per output file
-d        use numeric suffixes instead of alphabetic
-a 2      generate suffixes of length N (default 2)
```

**分割后**

```
$ ls
logstash.6.4.0.tar
logstash.6.4.0.tar.00
logstash.6.4.0.tar.01
logstash.6.4.0.tar.02
logstash.6.4.0.tar.03
logstash.6.4.0.tar.04
logstash.6.4.0.tar.05
logstash.6.4.0.tar.06
logstash.6.4.0.tar.07
logstash.6.4.0.tar.08
logstash.6.4.0.tar.09
logstash.6.4.0.tar.10
logstash.6.4.0.tar.11
```

## cat

```
$ cat logstash.6.4.0.tar.* > logstash.6.4.0.tar
```

## 参考链接

- [Linux 下将文件打包、压缩并分割成指定大小](https://blog.csdn.net/whu_zhangmin/article/details/45870077)




