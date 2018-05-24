---
title: 一次对 Spring Security UserDetailsService 的借鉴
date: 2018-05-23 17:18:26
categories: "更多"
tags:
    - Spring Security
keywords: Spring Security
---

> `org.springframework.security.core.userdetails.UserDetailsService`

## UserDetailsService

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

## 场景描述

- 应用中有一个单独的功能模块
- 需要尽可能地减少与应用中其他模块的交互
- 但是该模块又必须使用到其他模块持有的一条数据

### 不经大脑的实现方式

将获取该数据的实例，以构造参数等形式直接在模块中使用，如

```java
public class ThisModule {
    private OtherModule otherModule;

    public ThisModule(OtherModule otherModule) {
        this.otherModule = otherModule;
    }

    public void use() {
        System.out.println(otherModule.data());
    }
}
```

- **结果** 造成了不必要的耦合，这个模块如果想要单独提取出来，进行复用，ThisModule 这个类就成了累赘

### 参考 UserDetailsService 的实现方式

- 在模块中定义一个获取这条数据的接口，类似 UserDetailsService
- 在外部实现这个接口，并配置为 Spring Bean
- 在需要使用该数据的类中，将这个接口作为构造参数
- 以 @Autowire 等方式，通过 Spring 注入接口实现的 Bean
- 这样，需要复用这个模块时，新的应用只需要实现这个接口就可以了，当前模块不需要进行改动

```java
public class OtherModule implements ThisModuleInterface {
    @Override
    public String data() {
        return "Data";
    }
}
```

- **PS** 实现这个接口时，如果还需要用到另外一个独立模块 B 所提供的功能
    - 同理，注意不要将实现类放在模块 B 中，应该放在应用中的一个公共位置，起到类似适配器的功能

