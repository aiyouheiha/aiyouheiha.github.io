---
title: favorites
date: 2018-05-22 18:10:32
categories: "更多"
tags:
    - favorites
keywords: favorites
---

<!-- more -->

## Java

### List

```
Arrays.asList("On", "Off")
Collections.singletonList("Set")
```

### JSON

```
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonIgnoreProperties(ignoreUnknown = true)
```

```
/**
 * JSON 字段名为 interface 是 Java 修饰符，无法直接用于命名 Java 类的属性 <br>
 * 此时，通过 {@link JsonProperty} 可以进行转换
 */
@JsonProperty("interface")
public String interfaceName;
```

### lombok

```
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode(callSuper = true)
@ToString(callSuper = true)
```
