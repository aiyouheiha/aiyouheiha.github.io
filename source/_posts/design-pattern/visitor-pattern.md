---
title: 设计模式 - 访问者模式
date: 2017-10-11 20:05:57
categories: "设计模式"
tags:
    - 设计模式
    - Java
keywords: 设计模式, 访问者模式, Java
---

一种将算法与对象结构分离的软件设计模式

![Visitor Pattern UML](visitor-pattern/visitor-pattern-uml.png)

<!-- more -->

## 访问者模式

> 访问者模式（Visitor Pattern）是GoF提出的23种设计模式中的一种，属于行为模式。表示一个作用于某 **对象结构** 中的各元素的 **操作**，通过访问者模式，可以在不改变各元素类的前提下定义作用于这些元素的新操作。

### 访问者模式样例

- **对象结构**：被访问元素（Element）对象的集合
- **操作**：访问者（Visitor）中定义的访问Element对象的方法

#### 被访问元素 Element

```java
public interface Element {
    String info();
    void accept(Visitor visitor);
}

class Element1 implements Element {

    @Override
    public String info() {
        return "Element1";
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

class Element2 implements Element {

    @Override
    public String info() {
        return "Element2";
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}
```

#### 访问者 Visitor

```java
public interface Visitor {
    void visit(Element1 element1);
    void visit(Element2 element2);
}

class Visitor1 implements Visitor {

    @Override
    public void visit(Element1 element1) {
        System.out.println("Visitor1 " + element1.info());
    }

    @Override
    public void visit(Element2 element2) {
        System.out.println("Visitor1 " + element2.info());
    }
}

class Visitor2 implements Visitor {

    @Override
    public void visit(Element1 element1) {
        System.out.println("Visitor2 " + element1.info());
    }

    @Override
    public void visit(Element2 element2) {
        System.out.println("Visitor2 " + element2.info());
    }
}
```

#### 使用

```java
public class Client {
    public static void main(String[] args) {
        Element e1 = new Element1();
        Element e2 = new Element2();

        Visitor v1 = new Visitor1();
        Visitor v2 = new Visitor2();

        e1.accept(v1); // Visitor1 Element1
        e1.accept(v2); // Visitor2 Element1

        e2.accept(v1); // Visitor1 Element2
        e2.accept(v2); // Visitor2 Element2
    }
}
```

### 总结

访问者模式将 **对象结构** 和作用于结构上的 **操作** 解耦，方便操作相对自由地演化。拿前面的例子来讲，新增操作只需要添加Visitor的实现类即可，而不用改变对象结构（被访问元素集合）。

但需要注意的是，在Element数据结构变动时，访问者Visitor的整个继承体系都需要对应修改。因此，访问者模式适用于 **数据结构相对稳定** 以及 **算法易变化** 的系统。

也可以这样去理解，访问者模式是一种优化，它将 **对象结构** 中容易变化的 **操作** 抽象出来，通过访问者来执行这些操作。不难发现，如果不借助访问者模式，在这些容易变化的操作变动时，就不得不同时改变对象结构了。

这其实是这样的一个问题，在为一个现有的类增加新功能时，需要考虑到以下几点需求：
- 新功能会不会与现有功能出现兼容性问题？
- 以后会不会再需要添加？
- 如果类不允许修改代码怎么办？

面对这些需求，最好的解决方法就是使用访问者模式，将数据结构与算法解耦。

-------------------------------------------------------

## 方法分派

### 方法分派（Method Dispatch）

假设有一个万能许愿机（程序），可以 **实现愿望（方法）**，实现很多不同的 **愿望（方法参数）**。显然，每次要实现的愿望不同，其实现方式也不可能一样（多个方法）。但是，却都是 **实现愿望** 这一类方法。

再假设有两个许愿机，一个只需要喊出你的愿望（给出参数）就够了，另一个则必须先找到正确的许愿入口（方法）才能许愿。在可以100%实现愿望的前提下，哪个许愿机使用更为方便就相当明显了。

同理，如果能用同样的名字来命名同一类方法，则会有助于程序代码 **清晰** 的表达出语义。

这就引出了 **方法分派** 的概念，因为使用相同的方法名，就需要解决选用同名方法哪个版本的问题。有两种途径来解决这一问题：
1. 编译时由编译器选择（很多文章提出，可以认为这是一种静态分派）
2. 运行时进行 **方法分派**

在继续展开前，首先举例说明下两个概念：实际类型与静态类型，避免混淆。栗子如下：

```
// Child 变量p的实际类型
// Parent 变量p的静态类型
Parent p = new Child(); 
```

#### 编译时 - 重载（Overload）

> 参数的数量、类型等信息组成了函数的signature，使用同一个名字来命名signature不同的函数，称为函数重载（function overloading）。

```java
public class Foo {
    public void foo(int i) {
        System.out.println(i);
    }

    public void foo(int i, int j) {
        System.out.println(i + j);
    }

    public void foo(String s) {
        System.out.println(s);
    }

    public void foo(Parent p) {
        System.out.println("Parent");
    }

    public void foo(Child c) {
        System.out.println("Child");
    }

    public static void main(String[] args) {
        Foo foo = new Foo();
        Parent child = new Child();
        Child c = new Child();

        foo.foo(1); // 1
        foo.foo(1, 2); // 3
        foo.foo("Hello"); // Hello
        foo.foo(child); // Parent
        foo.foo(c); // Child
    }
}

class Parent {

}

class Child extends Parent {

}
```

函数重载是编译时概念，通过函数的signature，编译器已经有足够信息来判断应该使用哪个版本的方法。像上面的栗子，输入参数不同，对象foo的foo()方法打印结果也对应改变。

需要注意的是，参数类型是由变量声明的类型所决定的，也就是说编译器只会根据 **变量的静态类型** 来判断选择哪个版本的方法。也就是栗子中，下面这两行代码运行结果所体现出来的特性。

```
foo.foo(child); // Parent
foo.foo(c); // Child
```

这与Java是一种单分派语言相关，关于单分派这个概念，本文后面将会介绍。


#### 运行时 - 覆盖（Override）

同一个继承链上的不同类型可以拥有signature相同的虚方法，表现出多态。从语义上说，在编译时无法判断一个虚方法调用到底应该采用继承链上signature相同的哪个版本，所以要留待运行时进行分派（dispatch）。

```java
class Foo {
    public void foo(int i) {
        System.out.println(i);
    }
}

class Bar extends Foo {
    @Override
    public void foo(int i) {
        System.out.println(i + 1);
    }
}

public class Program {
    public static void main(String[] args) {
        Foo foo = new Bar();
        foo.foo(0); // 1
    }
}
```

栗子中，Foo与Bar便是同一个继承链上的不同类型，拥有signature相同的方法foo()，在方法调用时发生了分派，实际运行时使用了Bar的方法。

这里有一个和重载栗子中很类似的现象，重载栗子中，方法的调用只与 **方法参数** 的声明类型（静态类型）相关，而这个栗子中的foo对象（下面称其为 **方法接收者**），其方法的调用与变量foo的实际类型相关（栗子中其实际类型为Bar）。

使用静态类型还是实际类型，**方法参数** 和 **方法接收者** 有着不同的选择，如前文所讲，这与Java是一种单分派语言相关，下面将开始具体介绍。


### 方法的宗量

**方法接收者** 和 **方法参数** 统称为方法的宗量。 

根据分派基于宗量多少（接收者是一个宗量，参数是一个宗量），可以将分派分为 **单分派** 和 **多分派**。
- 单分派：根据一个宗量就可以知道调用目标（即应该调用哪个方法）。
- 多分派：需要根据多个宗量才能确定调用目标。


### 方法接收者

> 面向对象语言中经常会对函数调用的 **第一个参数** 做特殊处理，包括语法和语义都很特别。

- 语法：第一个参数不用写在参数列表，而是写在某种特殊符号之前（p.baz()的“.”），作为隐含参数。
- 语义：第一个参数被称为方法的接收者（receiver）。

也就是说方法接收者是一个特殊的方法参数，当然，为避免混淆，后面不会再称其为参数。

```
Parent p = new Child();
p.baz(); // p为方法baz调用的接收者
```

结合前面的栗子，可以做出这样的总结：
- 接收者（宗量1）的 **实际类型** 会参与到方法分派的判断中
- 方法参数（宗量2）
    - 其 **静态类型** 会影响方法选择，则为 **单一分派** + **方法重载**。
    - 以 **实际类型** 参与到方法分派的判断，则为 **多分派**。 


### 单分派与多分派的判断

```java
class Parent {}

class Child extends Parent {}

class Foobar {
    public void baz(Parent p1, Parent p2) {
        System.out.println("ParentParent");
    }

    public void baz(Parent p, Child c) {
        System.out.println("ParentChild");
    }

    public void baz(Child c1, Child c2) {
        System.out.println("ChildChild");
    }
}

public class Foo extends Foobar {
    @Override
    public void baz(Parent p1, Parent p2) {
        System.out.println("PP");
    }

    @Override
    public void baz(Parent p, Child c) {
        System.out.println("PC");
    }

    @Override
    public void baz(Child c1, Child c2) {
        System.out.println("CC");
    }
}
```

调用方法的接收者可以有如下三种情况的声明
- 通过 `foobar` 调用 `baz` 方法打印的结果是 `ParentParent` 这一类的输出
- 通过 `foo` 和 `bar` 调用 `baz` 方法打印的结果均为 `PP` 这一类的输出
- 调用方法后的输出，与方法接收者的实际类型有关
- **结论** 方法接收者的 **实际类型** 参与到了方法分派之中

```
Foobar foobar = new Foobar(); // 实际类型：Foobar
Foo foo = new Foo(); // 实际类型 Foo
Foobar bar = new Foo(); // 实际类型 Foo
```

类似的，方法参数也可以有如下三种情况的声明
- **结论** Java参数的实际类型并不会参与到方法分派，仅有参数的静态类型对调用结果产生了影响
    - Java是一种单分派语言
    - 多分派语言，则可以根据参数的实际类型进行方法的调用

```
Parent p = new Parent(); // 实际类型：Parent
Parent pc = new Child(); // 实际类型：Child
Child c = new Child(); // 实际类型：Child

foobar.baz(p, p); // ParentParent
foobar.baz(p, pc); // ParentParent >>> pc 的实际类型并没有影响到方法的执行结果
foobar.baz(p, c); // ParentChild
```

### 模拟双分派

> 访问者模式使得我们可以在传统的单分派（single-dispatch）语言（如Smalltalk、Java和C++）中模拟双分派技术。对于支持多分派（multiple-dispathc）的语言（如CLOS），访问者模式已经内置于语言特性之中了，从而不再重要。

#### 访问目标

> 一个由许多对象构成的对象结构，这些对象的类都拥有一个accept方法用来接受访问者对象；

- 上面栗子中的 `Parent && Child` 便可改造为一组访问目标

```
class Parent {
    public String accept(FoobarVisitor visitor) {
        return visitor.visit(this);
    }
}

class Child extends Parent {
    @Override
    public String accept(FoobarVisitor visitor) {
        // 注意此方法必须重写，否则this将无法指代Child的一个实例
        return visitor.visit(this);
    }
}
```

#### 访问者

> 访问者是一个接口，它拥有一个visit方法，这个方法对访问到的对象结构中不同类型的元素作出不同的反应；

```
class FoobarVisitor {

    public String visit(Parent parent) {
        return "Parent";
    }

    public String visit(Child child) {
        return "Child";
    }
}

class FooVisitor extends FoobarVisitor {
    @Override
    public String visit(Parent parent) {
        return "P";
    }

    @Override
    public String visit(Child child) {
        return "C";
    }
}
```

#### 通过访问者调用目标对象

```
class Foobar {
    private FoobarVisitor foobarVisitor = new FoobarVisitor();

    public void baz(Parent p1, Parent p2) {
        System.out.println(p1.accept(foobarVisitor) + p2.accept(foobarVisitor));
    }

    public void baz(Parent p, Child c) {
        System.out.println(p.accept(foobarVisitor) + c.accept(foobarVisitor));
    }

    public void baz(Child c1, Child c2) {
        System.out.println(c1.accept(foobarVisitor) + c2.accept(foobarVisitor));
    }
}

public class Foo extends Foobar {
    private FoobarVisitor foobarVisitor = new FooVisitor();

    @Override
    public void baz(Parent p1, Parent p2) {
        System.out.println(p1.accept(foobarVisitor) + p2.accept(foobarVisitor));
    }

    @Override
    public void baz(Parent p, Child c) {
        System.out.println(p.accept(foobarVisitor) + c.accept(foobarVisitor));
    }

    @Override
    public void baz(Child c1, Child c2) {
        System.out.println(c1.accept(foobarVisitor) + c2.accept(foobarVisitor));
    }
    
    public static void main(String[] args) {
        Foobar foobar = new Foobar(); // 实际类型：Foobar
        Foo foo = new Foo(); // 实际类型 Foo
        Foobar bar = new Foo(); // 实际类型 Foo

        Parent p = new Parent(); // 实际类型：Parent
        Parent pc = new Child(); // 实际类型：Child
        Child c = new Child(); // 实际类型：Child

        foobar.baz(p, p); // ParentParent
        foobar.baz(p, pc); // ParentChild
        foobar.baz(p, c); // ParentChild
    }
}
```

- 首先，根据前文 `foobar.baz(p, pc);` 可定还是调用的方法 `void baz(Parent p1, Parent p2)` 
- 但是，因为在方法内部使用了 `p2.accept(foobarVisitor)`，p2被分配到实际类型
- 然后在 `accept` 方法中 `foobarVisitor.visit(this)`，foobarVisitor也被分配到了实际类型
- 因此最后输出为 `ParentChild`，与之前不同，实现了模拟的双分派，基于方法接收者与方法参数

---------------------------------------------------------

## 引用

- [方法分派（method dispatch）的几个例子](http://rednaxelafx.iteye.com/blog/260206)
- [java方法调用之单分派与多分派（二）](http://blog.csdn.net/fan2012huan/article/details/51004615)
- [访问者模式](https://zh.wikipedia.org/wiki/%E8%AE%BF%E9%97%AE%E8%80%85%E6%A8%A1%E5%BC%8F)
- [Java开发中的23种设计模式详解(转)](http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html)

-------------------------------------------------------
