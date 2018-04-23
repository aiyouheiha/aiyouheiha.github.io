---
title: JSON Format
date: 2018-04-23 13:16:14
categories: "JSON"
tags:
    - JSON
keywords: JSON
---

## `@JsonInclude(JsonInclude.Include.NON_NULL)`

> Value that indicates that only properties with non-null values are to be included.

```java
/**
 * @author singoasher
 * @date 2018/4/23
 */
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)
public class Payload {
    private String accessToken;
    private String deviceId;
    private String deviceType;
    private String attribute;
    private String value;
    private ErrorCode errorCode;
    private String message;
    private Extensions extensions;
}
```

配置前

```json
{
    "accessToken": null,
    "deviceId": "34234",
    "deviceType": null,
    "attribute": null,
    "value": null,
    "errorCode": "DEVICE_IS_NOT_EXIST",
    "message": "device is not exist",
    "extensions": null
}
```

配置后

```json
{
    "deviceId": "34234",
    "errorCode": "DEVICE_IS_NOT_EXIST",
    "message": "device is not exist"
}
```

