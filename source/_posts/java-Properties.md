---
title: java-Properties
date: 2019-08-14 20:43:35
tags: [java,Properties]
---

#### Properties类

Java中有个比较重要的类`Java.util.Properties`，主要用于**读取**Java的**配置文件**。在Java中，其配置文件常为`.properties`文件，格式为`文本文件`，文件的内容的格式是`键=值`的格式，文本注释信息可以用`#`来注释。

提供了几个主要的方法：

- `getProperty ( String key)`，用指定的键，在**属性列表**中搜索属性
- `load ( InputStream inStream)`，通过对指定的文件进行装载，从**输入流**中读取属性列表
- `setProperty ( String key, String value)` ，调用 `Hashtable` 的方法 `put `来设置`键-值`对
- `store ( OutputStream out, String comments)`，将属性列表写入到指定的文件中
- ` clear ()`，清除所有装载的`键-值`对

<!--more-->

<br/>



```java
public static void main(String[] args) throws Exception {
    Properties pro = new Properties();
    pro.load(new FileReader("db.properties"));
    pro.setProperty("driver", "oracle.jdbc.driver.OracleDriver");
    pro.setProperty("user", "scott");
    pro.setProperty("pwd", "tiger");
    pro.store(new FileOutputStream(new File("db.properties")), "db配置");
}
```

