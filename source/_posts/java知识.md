---
title: java知识
date: 2019-03-07 19:08:54
tags: [java]
---

#### 类名与文件名

- 一个`Java`文件中只能有一个`public`类
- 如果文件中存在`public`类，文件名必须与`public`类名一致
- 如果文件中没有`public`类，文件名与类名可以不一致

注：一个源文件中，用`class关键字`定义了几个类，编译的时候就会产生几个`字节码`文件

<!--more-->

<br/>



#### 包名与文件名

`.java文件`中的**包名**必须与**物理文件夹**对应

```java
// Something.java 文件
package net.java.util;
public class Something{}
```

**物理存储位置**应该是：`net/java/util/Something.java ` 

注：

- 同一个包中的类名字不能相同 
- **同级包**下的类引用不需要`import`，可以直接调用
- `Java`编译器默认导入` java.lang `包 

<br/>



#### 主入口方法

```java
public static void main(String[] args) {}
```

1. `该java类`由`java虚拟机`调用，所以`java类`应把该方法暴露，故用`public` 
2. 既然由`JVM`调用该方法，肯定不能new 一个对象再由对象调用该方法，应直接由`JVM`调用故用`static` 
3. 给`JVM`返回东西是无意义的 ，故用`void` 
4. 括号里的参数是由`JVM`传给该方法的，具体可为：
   - 从`cmd控制台`传入
   - 从开发环境`IDE`配置参数传入

<br/>



#### 装箱与拆箱

所有的包装类**（Integer、Long、Byte、Double、Float、Short）**都是抽象类 `Number` 的子类。 

由编译器特别支持的包装称为**装箱**，所以当内置数据类型被当作对象使用的时候，编译器会**把内置类型装箱为包装类**。相似的，编译器也可以**把一个对象拆箱为内置类型** 。

```java
public class Test {
   public static void main(String args[]) {
      Integer x = 5;
      x =  x + 10;
      System.out.println(x); 
   }
}

// 当 x 被赋为整型值时，由于x是一个对象，所以编译器要对x进行装箱
// 然后，为了使x能进行加运算，所以要对x进行拆箱
```

<br/>



#### 创建数组

```
dataType[] arrayRefVar = new dataType[arraySize];
```

```
dataType[] arrayRefVar = {value0, value1, ..., valuek};
```

<br/>



#### 重写与重载

`重写(Override)`是子类对父类的允许访问的方法的实现过程进行重新编写, **返回值**和**形参**都不能改变

`重载(overloading)` 是在一个类里面，**方法名字**相同，而**参数**不同，返回类型可以相同也可以不同

<br/>

