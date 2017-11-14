---
title: Equals or Not
date: 2017-11-14 15:24:20
categories: "Java"
tags:
    - Java
keywords: equals, Java
---

简单介绍一些帮助判断 equals 的方法

<!-- more -->

## org.apache.commons.lang.builder.EqualsBuilder

FROM - [dddsample-core](https://github.com/citerus/dddsample-core)

```
@Override
public boolean sameEventAs(final HandlingEvent other) {
    return other != null && new EqualsBuilder().
            append(this.cargo, other.cargo).
            append(this.voyage, other.voyage).
            append(this.completionTime, other.completionTime).
            append(this.location, other.location).
            append(this.type, other.type).
            isEquals();
}
```

## java.util.Objects

FROM - 阿里巴巴 JAVA 开发手册

> 推荐使用 JDK7 引入的工具类 `java.util.Objects#equals`

```
public static boolean equals(Object a, Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```
