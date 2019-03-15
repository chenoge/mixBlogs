---
title: java-生成jar包
date: 2019-03-15 11:10:54
tags: [java,jar]
---

### 一、制作只含有字节码文件的jar包

我们先来看只含有字节码文件，即只含有`.class文件`的`jar包`怎么制作，这是最简单的形式

##### 1、最简单的jar包——直接输出hello

最终生成的jar包结构

> META-INF
>
> Hello.class

方法步骤

（1）用记事本写一个`Hello.java`的文件

```java
class Hello{
   public static void main(String[] agrs){
   	System.out.println("hello");
   }
}
```



（2）用命令行进入到该目录下，编译这个文件

```
 javac Hello.java 
```



（3）将编译后的`Hello.class`文件打成`jar包` 

```
 jar -cvf hello.jar Hello.class 
```

- `c` 表示要创建一个新的`jar包` 

- `v ` 表示创建的过程中在控制台输出创建过程的一些信息

- `f` 表示给生成的`jar包命名` 



（4）运行jar包

```
 java -jar hello.jar
 # 这时会报如下错误  hello.jar中没有主清单属性 
```



（5）添加`Main-Class`属性

用压缩软件打开`hello.jar`，会发现里面多了一个`META-INF`文件夹，里面有一个`MENIFEST.MF`的文件

```
Manifest-Version: 1.0
Created-By: 1.8.0_121 (Oracle Corporation)

```

添加` Main-Class: Hello` ，再次运行` java -jar hello.jar` ，此时成功在控制台看到 `hello ` 

<!--more-->

<br/>



##### 2、含有两个类的jar包——通过调用输出hello

最终生成的jar包结构

> META-INF
>
> Tom.class
>
> Hello.class



（1）用记事本写一个Hello.java和一个Tom.java的文件

```java
class Hello {
   public static void main(String[] agrs) {
   	System.out.println("hello");
   }
}
```

```java
class Tom {
	public static void speak() {
		System.out.println("hello");
	}
}
```



（2）编译

```
javac Hello.java 
```

此时`Hello.java`和`Tom.java`同时被编译，因为`Hello`中调用了`Tom` 



（3）打jar包，这次我们换一种方式**直接定义`Main-Class`** 

```
 Manifest-Version: 1.0
 Created-By: 1.8.0_121 (Oracle Corporation)
 Main-Class: Hello
 
```

事先准备好上述的`MENIFEST.MF文件`，并存放在`META-INF文件夹`下，此时打jar包的命令如下

```
jar -cvfm hello.jar META-INF\MENIFEST.MF Hello.class Tom.class 
```

该命令表示用第一个文件当做`MENIFEST.MF文件`，`hello.jar`作为名称，将`Hello.class`和`Tom.class`打成jar包。其中多了一个参数`m`，表示要定义`MENIFEST文件` 



（4）运行 `java -jar hello.jar` ，此时成功在控制台看到  hello ，成功

<br/>

##### 3、有目录结构的jar包——通过引包并调用输出hello

最终生成的jar包结构

> META-INF
>
> com
>
> ​	Tom.class
>
> Hello.class

我们将上一个稍稍变化一下，将`Tom类`放在`com包`下。

```
package com;
# Tom.java需要在第一行声明自己的包名
```

```
import com.Tom;
# Hello.java需要引入Tom这个类
```



（1）编译`Hello.java`



（2）打jar包，同样准备好`MENIFEST文件`

```
 jar -cvfm hello.jar META-INF\MENIFEST.MF Hello.class com 
 # 最后一个com表示把文件夹下的所有文件都打进jar包
```



（3）运行 `java -jar hello.jar`  ，此时成功在控制台看到  `hello` ，成功



（4）优化过程

我们注意到，`com包`下是有`Tom.java`源文件的，也被打进了jar包里，这样不太好，能不能优化一下`javac命令`，使所有的编译后文件编译到另一个隔离的地方呢，答案是可以的。

在编译`Hello.java`时，先新建一个`target文件夹`。然后我们用如下命令

```
 javac Hello.java -d target
 # 将所有编译后的文件，都放到target文件夹下
```

将`META-INF文件夹`也复制到`target目录`下，**进入这个目录**，输入如下命令

```
 jar -cvfm hello.jar META-INF\MENIFEST.MF * 
 # 最后一个位置变成了*，表示把当前目录下所有文件都打在jar包里
```

<br/>

[原文](https://www.cnblogs.com/mq0036/p/8566427.html#a11) 