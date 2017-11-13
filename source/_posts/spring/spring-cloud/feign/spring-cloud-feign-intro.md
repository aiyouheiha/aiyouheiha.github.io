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

[Demo Address](https://github.com/singoasher/spring-cloud-feign-demo)

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

**注意**
- 通过 `@FeignClient(name = "stores", url = "http://example.com/")` 创建，则只会向指定好的 url 发送请求
- 注解 `@PathVariable("storeId")` 中的参数 `storeId` 与在 Controller 中使用不尽相同，参数 **不可以** 省略

在 Controller 中使用

```java
@RestController
public class MyController {
    @Autowired
    private StoreClient storeClient;

    @GetMapping
    public ResponseEntity<List> getStores() {
        return ResponseEntity.ok(storeClient.getStores());
    }

    @PostMapping("/{value}")
    public ResponseEntity<String> update(@PathVariable String value, @RequestBody Store store) {
        return ResponseEntity.ok(storeClient.update(value, store));
    }
}
```

### About @FeignClient

- The serviceId attribute is now deprecated in favor of the name attribute.
- Previously, using the url attribute, did not require the name attribute. Using name is now required.
- Placeholders are supported in the name and url attributes.

```java
@FeignClient(name = "${feign.name}", url = "${feign.url}")
public interface StoreClient {
    //..
}
```

## 自定义配置

默认配置 `org.springframework.cloud.netflix.feign.FeignClientsConfiguration`

### 配置类

```java
@Configuration
public class FooConfiguration {
    @Bean
    public Contract feignContract() {
        return new feign.Contract.Default();
    }

    @Bean
    public BasicAuthRequestInterceptor basicAuthRequestInterceptor() {
        return new BasicAuthRequestInterceptor("user", "password");
    }
}
```

**NOTE**
- FooConfiguration does not need to be annotated with @Configuration. 
- However, if it is, then take care to exclude it from any @ComponentScan 
    - that would otherwise include this configuration as it will become the default source 
    - for feign.Decoder, feign.Encoder, feign.Contract, etc., when specified. 
- This can be avoided by putting it in a separate, non-overlapping package from any @ComponentScan or @SpringBootApplication, 
    - or it can be explicitly excluded in @ComponentScan.

### 配置使能 @FeignClient

```java
@FeignClient(name = "stores", configuration = FooConfiguration.class)
public interface StoreClient {
    //..
}
```

### 配置介绍

Spring Cloud Netflix provides the following beans by default for feign (BeanType beanName: ClassName):
- Decoder feignDecoder: ResponseEntityDecoder (which wraps a SpringDecoder)
- Encoder feignEncoder: SpringEncoder
- Logger feignLogger: Slf4jLogger
- Contract feignContract: SpringMvcContract
- Feign.Builder feignBuilder: HystrixFeign.Builder
- Client feignClient: if Ribbon is enabled it is a LoadBalancerFeignClient, otherwise the default feign client is used.

The OkHttpClient and ApacheHttpClient feign clients can be used by setting feign.okhttp.enabled or feign.httpclient.enabled to true, respectively, and having them on the classpath.

Spring Cloud Netflix does not provide the following beans by default for feign, but still looks up beans of these types from the application context to create the feign client:
- Logger.Level
- Retryer
- ErrorDecoder
- Request.Options
- Collection<RequestInterceptor>
- SetterFactory

### 重试

默认 NEVER_RETRY

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

### 契约

默认 SpringMvcContract

```
@Bean
@ConditionalOnMissingBean
public Contract feignContract(ConversionService feignConversionService) {
    return new SpringMvcContract(this.parameterProcessors, feignConversionService);
}
```

自定义，改为 Feign Contract.Default

```
@Bean
public Contract feignContract() {
    return new Contract.Default();
}
```

此时启用该配置的客户端，需要将 Spring MVC 注解，修改为 Feign 的默认注解

```java
@FeignClient(name = "stores", configuration = FooConfiguration.class)
public interface StoreClient {
    @RequestLine("GET /stores")
    String getStores();
}
```


## Use ThreadLocal bound variables in RequestInterceptor

You will need to either set the thread isolation strategy for Hystrix to `SEMAPHORE or disable Hystrix in Feign.

application.yml

```
# To disable Hystrix in Feign
feign:
  hystrix:
    enabled: false

# To set thread isolation to SEMAPHORE
hystrix:
  command:
    default:
      execution:
        isolation:
          strategy: SEMAPHORE
```

## Feign Hystrix Support

### 使能

If Hystrix is on the classpath and feign.hystrix.enabled=true, Feign will wrap all methods with a circuit breaker. 

```yaml
feign:
  hystrix:
    enabled: true
```

默认为 false

**WARNING**
- Prior to the Spring Cloud Dalston release, if Hystrix was on the classpath Feign would have wrapped all methods in a circuit breaker by default. 
- This default behavior was changed in Spring Cloud Dalston in favor for an opt-in approach.

To disable Hystrix support on a per-client basis create a vanilla Feign.Builder with the "prototype" scope, e.g.:

```
@Configuration
public class FooConfiguration {
    @Bean
	@Scope("prototype")
	public Feign.Builder feignBuilder() {
		return Feign.builder();
	}
}
```

### Feign Hystrix Fallbacks

增加 Fallback 类

```java
@Component
public class NonExistenceServiceFallback implements NonExistenceService {

    @Override
    public String hello() {
        return "Fallback Hello";
    }
}
```

为 @FeignClient 配置 fallback 属性

```java
@FeignClient(name = "non-existence", fallback = NonExistenceServiceFallback.class)
public interface NonExistenceService {
    @RequestMapping(method = RequestMethod.GET, value = "/hello")
    String hello();
}
```

**Feign and @Primary**
- When using Feign with Hystrix fallbacks, there are multiple beans in the ApplicationContext of the same type. 
- This will cause @Autowired to not work because there isn’t exactly one bean, or one marked as primary. 
- To work around this, Spring Cloud Netflix marks **all Feign instances** as @Primary, so Spring Framework will know which bean to inject. 

**说明**
- Spring Cloud Netflix marks all Feign instances as @Primary.
    - Spring Cloud Netflix 这样做了，但是在 IDEA 中，使用 @Autowire 时，IDE 依旧会给出下面的告警
        - Could not autowire. There is more than one bean of 'NonExistenceService' type.
        - **这并不会影响真正的使用**
    - **没什么用的提醒**
        - 给 NonExistenceService 接口加上 @Primary 可以避免这种 IDE 告警 **BUT** 这是个 **接口**，加上这个注解并 **不会** 有实际作用
        - 如果像下文 `@FeignClient(name = "hello", primary = false)` 关闭了自动 @Primary 注解，启动应用将会 **出错！！！**

In some cases, this may not be desirable. To turn off this behavior set the primary attribute of @FeignClient to false.

```java
@FeignClient(name = "hello", primary = false)
public interface HelloClient {
	// methods here
}
```

If one needs access to the cause that made the fallback trigger, one can use the fallbackFactory attribute inside @FeignClient.

```java
public class NonExistenceServiceFallback implements NonExistenceService {

    @Override
    public String hello() {
        return "Fallback Hello";
    }
}
```

```java
@Component
public class NonExistenceServiceFallbackFactory implements FallbackFactory<NonExistenceService> {
    @Override
    public NonExistenceService create(Throwable throwable) {
        return new NonExistenceServiceFallback();
    }
}
```

```java
@FeignClient(name = "non-existence", fallbackFactory = NonExistenceServiceFallbackFactory.class)
public interface NonExistenceService {
    @RequestMapping(method = RequestMethod.GET, value = "/hello")
    String hello();
}
```

**没什么用的提醒** 使用工厂顺便可以解决上文中提到的 IDE 告警问题（一个并不需要解决的问题哈）

**WARNING**
- There is a limitation with the implementation of fallbacks in Feign and how Hystrix fallbacks work. 
- Fallbacks are currently not supported for methods that return com.netflix.hystrix.HystrixCommand and rx.Observable.


## Feign Inheritance Support

Feign supports boilerplate apis via single-inheritance interfaces. This allows grouping common operations into convenient base interfaces.

公共接口

```java
public interface UserService {

    @RequestMapping(method = RequestMethod.GET, value ="/users/{id}")
    User getUser(@PathVariable("id") long id);
}
```

服务实现

```java
@RestController
public class UserResource implements UserService {

}
```

通过 @FeignClient 创建负载均衡器，以供客户端实现使用

```java
@FeignClient("users")
public interface UserClient extends UserService {

}
```

**NOTE**
- **不推荐** It is generally **NOT** advisable to share an interface between a server and a client. 
- **紧耦合/机制差异** It introduces tight coupling, and also actually does not work with Spring MVC in its current form (method parameter mapping is not inherited).


## Feign request/response compression

You may consider enabling the request or response GZIP compression for your Feign requests. You can do this by enabling one of the properties:

```
feign.compression.request.enabled=true
feign.compression.response.enabled=true
```

Feign request compression gives you settings similar to what you may set for your web server:

```
feign.compression.request.enabled=true
feign.compression.request.mime-types=text/xml,application/xml,application/json
feign.compression.request.min-request-size=2048
```

These properties allow you to be selective about the compressed media types and minimum request threshold length.


## Feign logging

A logger is created for each Feign client created. 
By default the name of the logger is the full class name of the interface used to create the Feign client. 
Feign logging only responds to the DEBUG level.

```yaml
logging:
  level:
    top.sinfonia.UserClient: DEBUG
```

The Logger.Level object that you may configure per client, tells Feign how much to log. Choices are:
- NONE, No logging (DEFAULT).
- BASIC, Log only the request method and URL and the response status code and execution time.
- HEADERS, Log the basic information along with request and response headers.
- FULL, Log the headers, body, and metadata for both requests and responses.

For example, the following would set the Logger.Level to FULL:

```
@Configuration
public class FooConfiguration {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```


## Creating Feign Clients Manually

In some cases it might be necessary to customize your Feign Clients in a way that is not possible using the methods above. In this case you can create Clients using the Feign Builder API. Below is an example which creates two Feign Clients with the same interface but configures each one with a separate request interceptor.

```java
@Import(FeignClientsConfiguration.class)
class FooController {

	private FooClient fooClient;

	private FooClient adminClient;

    @Autowired
	public FooController(
			Decoder decoder, Encoder encoder, Client client) {
		this.fooClient = Feign.builder().client(client)
				.encoder(encoder)
				.decoder(decoder)
				.requestInterceptor(new BasicAuthRequestInterceptor("user", "user"))
				.target(FooClient.class, "http://PROD-SVC");
		this.adminClient = Feign.builder().client(client)
				.encoder(encoder)
				.decoder(decoder)
				.requestInterceptor(new BasicAuthRequestInterceptor("admin", "admin"))
				.target(FooClient.class, "http://PROD-SVC");
    }
}
```

- In the above example FeignClientsConfiguration.class is the default configuration provided by Spring Cloud Netflix.
- PROD-SVC is the name of the service the Clients will be making requests to.

## 参考链接

- [Spring Cloud](http://cloud.spring.io/spring-cloud-static/Dalston.SR3/)




