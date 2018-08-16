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

- **备注**
    - `NON_NULL` 配置不会级联到子元素，如果未做配置，子元素中的 null 依旧会出现在 JSON 中

## `@JsonSerialize` and `@JsonDeserialize`

```
/**
 * @author singoasher
 * @date 2018/8/16
 */
@Data
@JsonIgnoreProperties(ignoreUnknown = true)
@JsonInclude(JsonInclude.Include.NON_NULL)
public class Demo implements Serializable {
    private String version;
    private String createUserName;

    @JsonSerialize(using = InstantJsonSerializer.class)
    @JsonDeserialize(using = InstantJsonDeserializer.class)
    private Instant createTime;
}
```

```
/**
 * @author singoasher
 * @date 2018/8/16
 */
public class InstantJsonSerializer extends JsonSerializer<Instant> {
    @Override
    public void serialize(Instant instant, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException, JsonProcessingException {
        jsonGenerator.writeString(instant.toString());
    }
}
```

```
/**
 * @author singoasher
 * @date 2018/8/16
 */
public class InstantJsonDeserializer extends JsonDeserializer<Instant> {
    @Override
    public Instant deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) throws IOException, JsonProcessingException {
        return Instant.parse(jsonParser.getText());
    }
}

```

配置前

```
{
    "createTime": {
        "nano": 505000000,
        "epochSecond": 1534407609
    }
}
```

配置后

```
{
    "createTime": "2018-08-16T08:28:58.275Z"
}
```








