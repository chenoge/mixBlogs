---
title: try-with-resources
date: 2019-07-31 21:16:46
tags: [try-with-resources]
---

##### 一、背景

在Java编程过程中，如果打开了外部资源（**文件、数据库连接、网络连接**等），我们必须在这些外部资源使用完毕后，手动关闭它们。因为外部资源不由**JVM**管理，**无法享用JVM的垃圾回收机制**。

如果我们不在编程时确保在正确的时机关闭外部资源，就会导致**外部资源泄露**，紧接着就会出现**文件被异常占用**，数据库连接过多导致**连接池溢出**等诸多很严重的问题。

<br/>



##### 二、传统的资源关闭方式

为了确保外部资源一定要被关闭，通常关闭代码被写入finally代码块中，当然我们还必须注意到关闭资源时可能抛出的异常，于是变有了下面的经典代码：

```java
public static void main(String[] args) {
    FileInputStream inputStream = null;
    try {
        inputStream = new FileInputStream(new File("test"));
        System.out.println(inputStream.read());
    } catch (IOException e) {
        throw new RuntimeException(e.getMessage(), e);
    } finally {
        if (inputStream != null) {
            try {
                inputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e.getMessage(), e);
            }
        }
    }
}
```

<!--more-->

在C++中，我们可以把关闭资源的代码放在**析构函数**中，让**外部资源的关闭行为**与**外部资源的句柄对象的生命周期**关联，当外部资源的句柄对象生命周期终结时，外部资源的关闭行为将被自动调用。这样不仅更加符合面向对象的编程理念，也让代码更加简洁易懂。

<br/>



##### 三、JDK7及其之后的资源关闭方式

当一个外部资源的**句柄对象**实现了`AutoCloseable`接口，那么就可以将上面的板式代码简化为如下形式：

```java
public static void main(String[] args) {
    try (FileInputStream inputStream = new FileInputStream(new File("test"))) {
        System.out.println(inputStream.read());
    } catch (IOException e) {
        throw new RuntimeException(e.getMessage(), e);
    }
}
```

将外部资源的句柄对象的创建放在try关键字后面的括号中，当这个try-catch代码块执行完毕后，Java会确保外部资源的close方法被调用。

<br/>



#### 四、原理

`try-with-resource`并不是JVM虚拟机的新增功能，只是JDK实现了一个语法糖，当你将上面代码反编译后会发现，其实对JVM虚拟机而言，它看到的依然是之前的写法：

```java
public static void main(String[] args) {
    try {
        FileInputStream inputStream = new FileInputStream(new File("test"));
        Throwable var2 = null;

        try {
            System.out.println(inputStream.read());
        } catch (Throwable var12) {
            var2 = var12;
            throw var12;
        } finally {
            if (inputStream != null) {
                if (var2 != null) {
                    try {
                        inputStream.close();
                    } catch (Throwable var11) {
                        var2.addSuppressed(var11);
                    }
                } else {
                    inputStream.close();
                }
            }

        }

    } catch (IOException var14) {
        throw new RuntimeException(var14.getMessage(), var14);
    }
}
```

当对外部资源进行处理时，如果遭遇了异常，且在随后的关闭外部资源过程中，又遭遇了异常，那么**你catch到的将会是对外部资源进行处理时遭遇的异常**，关闭资源时遭遇的异常将被“**抑制**”但不是丢弃，通过异常的`getSuppressed`方法，可以提取出被抑制的异常。

<br/>



##### 五、总结

1、当一个外部资源的句柄对象实现了`AutoCloseable`接口，JDK7中便可以利用`try-with-resource`语法更优雅的关闭资源，消除板式代码。

2、`try-with-resource`时，如果对外部资源的处理和对外部资源的关闭均遭遇了异常，“**关闭异常**”将被抑制，“**处理异常**”将被抛出，但“**关闭异常**”并没有丢失，而是存放在“**处理异常**”的被抑制的**异常列表**中。

<br/>