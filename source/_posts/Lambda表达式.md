---
title: Lambda表达式
date: 2019-03-29 20:37:16
tags: [Lambda]
---

```java
Runnable noArguments = () -> System.out.println("Hello World");
// Runnable
```

```java
ActionListener oneArgument = event -> System.out.println("button clicked");
// ActionListener
```

```java
Runnable multiStatement = () -> {
    System.out.print("Hello");
    System.out.println(" World");
};
// Runnable
```

```java
BinaryOperator<Long> add = (x, y) -> x + y;
// BinaryOperator
```

```java
BinaryOperator<Long> addExplicit = (Long x, Long y) -> x + y;
// BinaryOperator
```

注：Lambda 表达式都可以扩写为原始的`匿名类`形式

