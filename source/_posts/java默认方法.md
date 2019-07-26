---
title: java默认方法
date: 2019-07-26 13:39:48
tags: [default]
---

#### 默认方法

Java 8 引入了新的语言特性——默认方法（Default Methods）。默认方法是**在接口中的方法名前加上了 `default` 关键字**的**实现**方法。

```java
interface InterfaceA {
    default void foo() {
        System.out.println("InterfaceA foo");
    }
}

class ClassA implements InterfaceA {
    
}

public class Test {
    public static void main(String[] args) {
        new ClassA().foo(); // 打印：“InterfaceA foo”
    }
}
```

在 java 8 之前，接口与其实现类之间的**耦合度**太高，当需要为一个接口添加方法时，所有的实现类都必须随之修改。**默认方法**解决了这个问题，它可以**为接口添加新的方法，而不会破坏已有的接口的实现**。

这在 lambda 表达式作为 java 8 语言的重要特性而出现之际，为升级旧接口且保持向后兼容（backward compatibility）提供了途径。

<!--more-->

<br/>

- `default` 关键字只能在接口中使用（以及用在 `switch` 语句的 `default` 分支），不能用在抽象类中。
- 接口默认方法不能覆写 `Object` 类的 `equals`、`hashCode` 和 `toString` 方法。
- 接口中的静态方法必须是 `public` 的，`public` 修饰符可以省略，`static` 修饰符不能省略

