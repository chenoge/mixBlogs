---
title: java泛型
date: 2019-06-14 13:28:20
tags: [java,泛型]
---

#### 泛型

- 泛型的**类型参数声明部分(`<T>` )**可以包含一个或多个**类型参数**，参数间用逗号隔开。

- **类型参数**只能代表**引用型类型**，不能是**原始类型**。

<br/>



#### 泛型方法

- **类型参数声明部分**在方法返回类型之前

```java
// a 包含 b
public static <T> Boolean arrayIncludes(T[] a, T[] b) {
    for (T type : b) {
        if (!Arrays.asList(a).contains(type)) {
            return false;
        }
    }
    return true;
}
```

<!--more-->

<br/>



#### 泛型类

- **类型参数声明部分**在类名之后

```java
public class Box<T> {
  private T t;
  public void add(T t) {
    this.t = t;
  }
}
```

<br/>



#### 泛型接口

- **类型参数声明部分**在接口名之后

```java
public interface Generator<T> {
    public T next();
}
```

<br/>



#### 泛型通配符

- `? `通配符只有在**修饰一个变量**时会用到，使用它可方便地引用包含了多种类型的泛型
- `<? extends T> `类型的上界，表示`类型参数(?)`可能是T或是T的子类
- `<? super T> `类型下界，表示`类型参数(?)`是T的超类型（父类型），直至Object

```java
// 无限定通配符
// List<?>等同于 List<? extends Object>
public static void getData(List<?> data) {
    System.out.println("data :" + data.get(0));
}

// 子类限定通配符
public static void getNumberData(List<? extends Number> data) {
    System.out.println("data :" + data.get(0));
}

// 父类限定通配符
// 只能接受Number及其三层父类类型
public static void getNumberData(List<? super Number> data) {
    System.out.println("data :" + data.get(0));
}
```

<br/>