---
title: Vim 命令
date: 2018-01-09 14:03:27
categories: "更多"
tags:
    - Vim
keywords: Vim
---

这里会简单介绍 Vim 操作命令（持续更新……）

<!-- more -->

## 整行移动

```
0   移动到行首
$   移动到行末
+   移动到下一行开头
-   移动到上一行开头
```

## 多窗口

```
control + w + v                新建一个纵向窗口，打开当前文件
:vs                            等价于 [control + w + v]
:vs path                       新建一个纵向窗口，打开指定 path
control + w >>> w              多窗口间切换，即先使用组合键 [control + w] 之后再次点击 [w]
control + w >>> control + w    等价于 [control + w >>> w]
```

## 参考链接

- [轻快的VIM（一）：移动](https://www.cnblogs.com/nerxious/archive/2012/12/21/2827303.html)
- [VIM 用户手册 usr_08.txt](http://vimcdoc.sourceforge.net/doc/usr_08.html#usr_08.txt)

