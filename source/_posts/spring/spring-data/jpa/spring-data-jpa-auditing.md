---
title: Spring Data JPA Auditing
date: 2017-11-13 14:48:46
categories: "Spring Data"
tags:
    - Spring Boot
    - Java
    - JPA
keywords: Spring Boot, JPA, Java, @EnableJpaAuditing
---

通过样例，简单介绍 Spring Data JPA Auditing 功能（基于 Spring Boot）
- 创建/更新数据的同时，生成数据创建/修改时间
- 创建/更新数据的同时，生成数据创建/修改人

<!-- more -->

## 开启

假设 `CurrentUserInfo` 类为一个用来获取访问用户信息的类

```java
public class CurrentUserInfo {
    public static String getUserName() {
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
        String userName = request.getHeader("User-Name");
        return userName == null ? "" : userName;
    }
}
```

对应开启 **审计** 功能的配置类，可参考如下代码
- `@EnableJpaAuditing` 用于开启审计功能
- `auditorAware()` 方法用于提供 `CurrentAuditor`，实现了 `AuditorAware` 接口

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@Configuration
@EnableJpaAuditing
public class JpaConfig {
    @Bean
    public AuditorAware<String> auditorAware() {
        return CurrentUserInfo::getUserName;
    }
}
```

## 使用

如下 `GeneralEntity` 是一个提供给实体类继承的，包含公共属性的抽象类
- **PS** 公共类 `GeneralEntity` 只是测试使用，感觉会方便些许，暂不确定后果，慎重使用

其中
- `@EntityListeners({AuditingEntityListener.class})` 
    - 用于设定所回调的 `EntityListener`，通过它来捕获审计信息
    - 可以认为是在对应 Entity 或 Mapped Superclass 上启用审计功能
- `@CreatedDate` 创建时间
- `@CreatedBy` 创建人
- `@LastModifiedDate` 修改时间
- `@LastModifiedBy` 修改人

```java
import lombok.Data;
import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.util.Date;

@Data
@MappedSuperclass
@EntityListeners({AuditingEntityListener.class})
public abstract class GeneralEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Temporal(TemporalType.TIMESTAMP)
    @Column(name = "create_date", updatable = false)
    @CreatedDate
    private Date createDate;

    @Column(name = "create_by", updatable = false)
    @CreatedBy
    private String createdBy;

    @Temporal(TemporalType.TIMESTAMP)
    @Column(name = "last_modified_date")
    @LastModifiedDate
    private Date lastModifiedDate;

    @Column(name = "last_modified_by")
    @LastModifiedBy
    private String lastModifiedBy;
}
```

以上是在 Mapped Superclass 中使用，在 Entity 中使用并无差别，不做赘述

