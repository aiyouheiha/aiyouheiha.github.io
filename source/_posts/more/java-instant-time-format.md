---
title: Java Instant Time Format
date: 2018-08-24 15:28:45
categories: "Java"
tags:
    - Java
keywords: Java
---

## 错误代码

```
System.out.println(Instant.parse("2018-08-24T15:26:09.258+08:00"));

// Exception in thread "main" java.time.format.DateTimeParseException: 
//     Text '2018-08-24T15:26:09.258+08:00' could not be parsed at index 23
```

## 正确的方式

```
System.out.println(Instant.from(ZonedDateTime.parse("2018-08-24T15:26:09.258+08:00")));

// 2018-08-24T07:26:09.258Z
```

## more

```
System.out.println(Instant.now().atZone(ZoneId.of("+08")).toString());
System.out.println(Instant.now().atZone(ZoneId.systemDefault()).toString());
System.out.println(ZonedDateTime.now(ZoneId.systemDefault()));

// 2018-08-24T15:39:45.172+08:00
// 2018-08-24T15:39:45.178+08:00[Asia/Shanghai]
// 2018-08-24T15:39:45.264+08:00[Asia/Shanghai]

System.out.println(Instant.from(ZonedDateTime.parse("2018-08-24T15:37:07.048+08:00[Asia/Shanghai]")));

// 2018-08-24T07:37:07.048Z

System.out.println(Instant.parse("2018-08-24T07:26:09.258Z"));

// 2018-08-24T07:26:09.258Z
```

