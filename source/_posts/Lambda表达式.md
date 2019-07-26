---
title: Java Lambda表达式
date: 2019-03-29 20:37:16
tags: [Lambda,Java]
---

#### 语法

lambda 表达式的语法格式如下：

```
(parameters) -> expression

或

(parameters) ->{ statements; }
```

- **可选类型声明：**不需要声明参数类型，编译器可以统一识别参数值
- **可选的参数圆括号：**一个参数无需定义圆括号，但多个参数需要定义圆括号
- **可选的大括号：**如果主体包含了一个语句，就不需要使用大括号
- **可选的返回关键字：**如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值

<!--more-->

<br/>



```java
public class Java8Tester {
   public static void main(String args[]){
      Java8Tester tester = new Java8Tester();
       
      // 类型声明
      MathOperation addition = (int a, int b) -> a + b;
        
      // 不用类型声明
      MathOperation subtraction = (a, b) -> a - b;
        
      // 大括号中的返回语句
      MathOperation multiplication = (int a, int b) -> { return a * b; };
        
      // 没有大括号及返回语句
      MathOperation division = (int a, int b) -> a / b;
        
      System.out.println("10 + 5 = " + tester.operate(10, 5, addition));
      System.out.println("10 - 5 = " + tester.operate(10, 5, subtraction));
      System.out.println("10 x 5 = " + tester.operate(10, 5, multiplication));
      System.out.println("10 / 5 = " + tester.operate(10, 5, division));
   }
    
   interface MathOperation {
      int operation(int a, int b);
   }
    
   private int operate(int a, int b, MathOperation mathOperation){
      return mathOperation.operation(a, b);
   }
}
```

注：Lambda表达式只支持函数式接口（只有一个抽象方法）

<br/>



#### **@FunctionalInterface**注解

Java 8为函数式接口引入了一个新注解@FunctionalInterface，主要用于**编译级错误检查**，加上该注解，当你写的接口不符合函数式接口定义的时候，编译器会报错。

```java
@FunctionalInterface
interface GreetingService {
    void sayMessage(String message);
}
```

注：@FunctionalInterface对于接口是不是函数式接口没有影响，该注解知识提醒编译器去检查该接口**是否仅包含一个抽象方法**。

<br/>



##### 函数式接口里允许定义默认方法

函数式接口里是可以包含默认方法，因为默认方法不是抽象方法，其有一个默认实现，所以是符合函数式接口的定义的：

```java
@FunctionalInterface
interface GreetingService {
    void sayMessage(String message);
    
    default void doSomeMoreWork1() {
        // Method body
    }
    
    default void doSomeMoreWork2() {
        // Method body
    }
}
```

<br/>



##### 函数式接口里允许定义静态方法

函数式接口里是可以包含静态方法，因为静态方法不能是抽象方法，是一个已经实现了的方法，所以是符合函数式接口的定义的：

```java
@FunctionalInterface
interface GreetingService {
    void sayMessage(String message);
    static void printHello() {
        System.out.println("Hello");
    }
}
```

<br/>



##### 函数式接口里允许定义`java.lang.Object`里的public方法

- 函数式接口里是可以包含`Object`里的`public`方法
- 任何一个函数式接口的实现，默认都继承了`Object`类

```java
@FunctionalInterface
interface GreetingService {
    void sayMessage(String message);
    
    @Override
    boolean equals(Object obj);
}
```

<br/>