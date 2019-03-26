---
title: java-static块
date: 2019-03-26 12:59:40
tags: [static]
---

#### `static块`

`static{}`，会**在类被加载的时候执行且仅会被执行一次**，一般用来初始化静态变量和调用静态方法

```java
public class TestStatic {
    static {
        System.out.println(1);
    }
    
    static {
        System.out.println(2);
    }
    
    public static void main(String args[]) {
        System.out.println(4);
    }

    static {
        System.out.println(3);
    }
}
```

<!--more-->

<br/>



#### 类加载特性

1. 在虚拟机的生命周期中一个类只被加载一次
2. 类加载的原则：延迟加载，能少加载就少加载，因为虚拟机的空间是有限的

<br/>



#### 类加载的时机

1. 第一次创建对象要加载类
2. 调用静态方法时要加载类,访问静态属性时会加载类
3. 加载子类时必定会先加载父类
4. 创建对象引用不加载类
5. 子类调用父类的静态方法时
   1. 当子类没有覆盖父类的静态方法时，只加载父类，不加载子类
   2. 当子类有覆盖父类的静态方法时，既加载父类，又加载子类

6. 访问静态常量，如果编译器可以计算出常量的值，则不会加载类,例如:`public static final int a =123;`否则会加载类,例如:`public static final int a = math.PI`。

<br/>

