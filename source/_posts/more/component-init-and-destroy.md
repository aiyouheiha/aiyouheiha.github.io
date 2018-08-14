---
title: Component - init and destroy
date: 2018-08-14 18:46:19
categories: "更多"
tags:
    - Spring
keywords: Spring
---

## Component

```
@Component
public class DemoComponent implements InitializingBean, DisposableBean {
    @PostConstruct
    public void init() {
        System.out.println("PostConstruct");
    }

    @PreDestroy
    public void preDestroy() {
        System.out.println("PreDestroy");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("DisposableBean");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean");
    }
}
```

## Test

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class DemoComponentTest extends BaseTest {
    @Test
    public void test() {
        System.out.println("test");
    }
}
```

## Output

```
PostConstruct
InitializingBean
test
PreDestroy
DisposableBean
```