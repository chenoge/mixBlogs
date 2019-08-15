---
title: 读取resources资源文件
date: 2019-08-15 20:30:55
tags: [resources,getResource,getClassLoader]
---

由Maven构建的web工程，主代码放在`src/main/java`路径下，资源放在`src/main/resources`路径下，当构建**jar包 或 war包**时，JVM虚拟机会自动编译java文件为class文件存放在 `target/classes`目录下，`resource`资源下的文件会**原封不动的拷贝一份到** `target/classes` 目录下。

<!--more-->

<br/>



##### `Class.getResource(String path)`

- path不以`/`开头时，默认是从此类所在的包下取资源

  ```java
  // 当前类(class)所在的包目录 (指定资源名，可以获取该包下面的其他资源)
  System.out.println(this.getClass().getResource(""));
  // 输出： file:/D:/ds-epsm-data-provider/target/classes/com/datastory/controller/
  ```

- path 以`/`开头时，则是从`ClassPath`根下获取

  ```java
  // class path根目录 (指定资源名，可以获取根目录下面的资源)
  System.out.println(this.getClass().getResource("/"));
  // 输出： file:/D:/ds-epsm-data-provider/target/classes/
  ```

注：`Class.getResourceAsStream(String path)`方法，相当于用`getResource()`取得File文件后，再`new InputStream(file)`一样的结果 

<br/>



##### `Class.getClassLoader().getResource(String path)`

- `path`是从`ClassPath`根下获取，并且不能以`/`开头

  ```java
  System.out.println(this.getClass().getClassLoader().getResource(""));
  // 输出： file:/D:/ds-epsm-data-provider/target/classes/
  
  System.out.println(this.getClass().getClassLoader().getResource("/"));
  // 输出： null
  ```

<br/>