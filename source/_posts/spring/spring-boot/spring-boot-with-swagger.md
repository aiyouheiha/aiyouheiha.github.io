---
title: Spring Boot with Swagger
date: 2017-11-21 18:40:43
categories: "Spring Boot"
tags:
    - Spring Boot
    - Swagger
    - RESTful API
keywords: Spring Boot, Swagger, RESTful API
---

> THE WORLD'S MOST POPULAR API TOOLING

<!-- more -->

## 依赖

```
<!-- swagger -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.6.1</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.6.1</version>
</dependency>
```

## 配置

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("top.ashman.sample.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("API Title - Powered by Swagger")
                .description("Description of API")
                .termsOfServiceUrl("sample.ashman.top")
                .version("v1")
                .build();
    }
}
```

## 使用

```java
@Api(value = "Demo API")
@RestController
@RequestMapping(path = "/v1/demo")
public class DemoController {
    @Autowired
    private DemoService demoService;

    @ApiOperation(value = "Create Demo")
    @RequestMapping(method = RequestMethod.POST)
    public ResponseEntity create(@RequestBody DemoCommand demoCommand) throws URISyntaxException {
        Demo demo = demoService.create(demoCommand);
        URI uri = new URI("/v1/demo/" + demo.getId());
        return ResponseEntity.created(uri).build();
    }
}
```

## 一个遇到的问题

代码实现时，针对 `@RequestMapping` 做了切面方法，用来记录请求数据和返回值。
在切面方法中，使用了 `Method java.lang.Class#getMethod(String name, Class<?>... parameterTypes)` 方法。

关于这个方法，其注释中注明了这是一个用于获取 public 方法的方法：
- Returns a {@code Method} object that reflects the specified **public** member method of the class or interface represented by this {@code Class} object. 

而 Swagger `springfox.documentation.swagger.web.ApiResourceController` 中，
`@RequestMapping` 注解下的 uiConfiguration 方法并非一个 public 方法

```
@RequestMapping(value = "/configuration/ui")
@ResponseBody
ResponseEntity<UiConfiguration> uiConfiguration() {
return new ResponseEntity<UiConfiguration>(
    Optional.fromNullable(uiConfiguration).or(UiConfiguration.DEFAULT), HttpStatus.OK);
}
```

这导致了切面调用的 `getMethod` 方法无法正常执行，抛出 `NoSuchMethodException` 异常：

```
// getMethod 方法代码片段
if (method == null) {
    throw new NoSuchMethodException(getName() + "." + name + argumentTypesToString(parameterTypes));
}
```

也就导致无法正常使用 Swagger 的 UI 功能。


## 参考链接

- [World's Most Popular API Framework | Swagger](https://swagger.io/)
- [Spring Boot 集成 Swagger2 - 构建优雅的 Restful API](http://blog.csdn.net/forezp/article/details/71023536)


