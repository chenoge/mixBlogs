---
title: java类加载器
date: 2019-03-15 10:31:05
tags: [java,ClassLoader,类加载器]
---

### 类加载器

类加载器负责将`.class文件`加载到内存中，并为类生成一个`java.lang.Class`实例。

一旦一个类被加载入`JVM`中，同一个类就不会被再次加入了。在`JVM`中用来判断类的唯一性标识是：**类名、类所在的包名和类加载器**。

当`JVM`启动时，会形成由三个类加载器组成的初始类加载器层次结构：

- `BootStrap ClassLoader`：根类加载器
- `Extension ClassLoader`：扩展类加载器
- `System ClassLoader`：系统类加载器

<!--more-->

<br/>

##### 根类加载器 （bootstrap class loader）：

它用来加载 Java 的核心库(`jre/lib/rt.jar`)，是用原生`C++`代码来实现的，并不继承自`java.lang.ClassLoader`。**没有父加载器** 

<br/>

##### 扩展类加载器（extensions class loader）：

它用来加载 Java 的扩展库(`jre/ext/*.jar`)。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。 **父加载器为根类加载器** 

扩展类加载器是纯Java类，是**java.lang.ClassLoader**类的子类

<br/>

##### 系统类加载器（system class loader）：

它根据 Java 应用的类路径（`CLASSPATH`）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 `ClassLoader.getSystemClassLoader()`来获取它。**父加载器为扩展类加载器 ** 

系统类加载器是纯Java类，是**java.lang.ClassLoader**类的子类

<br/>

##### 自定义类加载器（custom class loader）：

除了系统提供的类加载器以外，开发人员可以通过继承 `java.lang.ClassLoader`类的方式实现自己的类加载器，以满足一些特殊的需求。

<br/>

### 类加载的父委托机制

从`JDK 1.2`版本开始，类的加载过程采用**父亲委托机制**，这种机制能更好地保证Java平台的安全。

在父委托机制中，除了Java虚拟机自带的**根类加载器**以外，其余的类加载器都**有且只有一个**父加载器，各个加载器按照父子关系形成了树形结构。

当Java程序请求`加载器loader1`加载`Sample类`时，`loader1`首先委托自己的父加载器去加载`Sample类`，若父加载器能加载，则由父加载器完成加载任务，否则才由`loader1`本身加载`Sample类`。

注：

- **这里的父加载器概念并不是指类的继承关系，子加载器不一定继承了父加载器（其实是组合的关系）** 

- **每个加载器都优先尝试用父类加载，若父类不能加载则自己尝试加载；若成功则返回Class对象给子类，若失败则告诉子类让子类自己加载。所有都失败则抛出`ClassNotFoundException`异常** 