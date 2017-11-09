---
title: Spring Cloud Feign 简介
date: 2017-11-09 18:26:37
categories: "Spring Cloud"
tags:
    - Spring Cloud
    - Spring Boot
    - Java
    - Feign
keywords: Spring Cloud, Spring Boot, Java, Feign
---

>  It makes writing web service clients easier. 

声明式服务调用

Ribbon + RestTemplate 以拼接字符串的形式构造 URL
- 低效
- 难以维护


<!-- more -->

## 依赖

```xml
<dependencies>
    <dependency>
    	<groupId>org.springframework.cloud</groupId>
    	<artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    <dependency>
    	<groupId>org.springframework.cloud</groupId>
    	<artifactId>spring-cloud-starter-feign</artifactId>
    </dependency>
</dependencies>
```

## 使用

注解 `@EnableFeignClients`

```java
@EnableFeignClients
@EnableDiscoveryClient
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

通过 `@FeignClient("stores")` 创建 Ribbon 负载均衡器，`stores` 为用于创建 Ribbon 负载均衡器的客户端名称

```java
@FeignClient("stores")
public interface StoreClient {
    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    List<Store> getStores();

    @RequestMapping(method = RequestMethod.POST, value = "/stores/{storeId}", consumes = "application/json")
    Store update(@PathVariable("storeId") Long storeId, Store store);
}
```

通过 `@FeignClient(name = "stores", url = "http://example.com/")` 创建，则只会向指定好的 url 发送请求

**注意** `@PathVariable("storeId")` 中的参数 `storeId` 与在 Controller 中使用不尽相同，**不可以** 省略




### 调用

```java
@RestController
@RequestMapping("/v1/consumer")
public class ConsumerController {
    @Autowired
    private ProviderService providerService;

    @GetMapping
    public String get() {
        return providerService.get();
    }

    @GetMapping("/{value}")
    public String getValue(@PathVariable String value) {
        return providerService.getValue(value);
    }
}
```

## 自定义配置

默认配置 `org.springframework.cloud.netflix.feign.FeignClientsConfiguration`

### 重试

默认不进行重试

```
@Bean
@ConditionalOnMissingBean
public Retryer feignRetryer() {
    return Retryer.NEVER_RETRY;
}
```

自定义

```
@Bean
public Retryer feignRetryer() {
    return new Retryer.Default(100, SECONDS.toMillis(1), 3);
}
```





