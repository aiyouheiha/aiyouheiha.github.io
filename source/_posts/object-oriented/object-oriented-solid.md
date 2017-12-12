---
title: SOLID (object-oriented design)
date: 2017-12-11 11:24:17
categories: "Object Oriented"
tags:
    - Object Oriented
    - SOLID
keywords: Object Oriented, SOLID
---

**SOLID** - 助记缩写词，对应五个程序设计原则

| Initial | Name                            | 名称         |
| :------ | :------------------------------ | :----------- |
| S       | Single responsibility principle | 单一职责原则 |
| O       | Open/closed principle           | 开闭原则     |
| L       | Liskov substitution principle   | 里氏替换原则 |
| I       | Interface segregation principle | 接口分离原则 |
| D       | Dependency inversion principle  | 依赖倒置原则 |

<!-- more -->

## 单一职责原则

> A class should have only a single responsibility (i.e. changes to only one part of the software's specification should be able to affect the specification of the class).

定义
- 每个模块或类 should have 职责 over a single part of 软件提供的功能
- 这个职责应该能够被类完全封装
- All its services should be narrowly aligned with that responsibility.

> Robert C. Martin expresses the principle as, "A class should have only one reason to change."

**职责** = **引起类变更的原因**

### 职责的划分

职责的划分是根据需求定的，同一个类（接口）的设计，在不同的需求里面，可能职责的划分并不一样

接口的粒度要设计到哪一步，取决于需求的变更程度，或者说取决于需求的复杂度


## 开闭原则



## 参考链接

- [SOLID (object-oriented design)](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))
- [Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)
- [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin)
- [小话设计模式原则之：单一职责原则SRP](https://zhuanlan.zhihu.com/p/24198903)
