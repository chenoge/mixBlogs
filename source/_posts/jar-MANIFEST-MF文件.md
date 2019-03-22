---
title: jar-MANIFEST.MF文件
date: 2019-03-15 14:27:27
tags: [jar,MANIFEST.MF]
---

打开Java的`JAR文件`我们经常可以看到文件中包含着一个`META-INF目录`， 这个目录下会有一些文件，其中必有一个`MANIFEST.MF`，这个文件描述了该`Jar文件`的很多信息。

下面将详细介绍`MANIFEST.MF文件`的内容，先来看`struts.jar`中包含的`MANIFEST.MF`文件内容：

```
Manifest-Version: 1.0
Created-By: Apache Ant 1.5.1
Extension-Name: Struts Framework
Specification-Title: Struts Framework
Specification-Vendor: Apache Software Foundation
Specification-Version: 1.1
Implementation-Title: Struts Framework
Implementation-Vendor: Apache Software Foundation
Implementation-Vendor-Id: org.apache
Implementation-Version: 1.1
Class-Path:  commons-beanutils.jar commons-collections.jar commons-digester.jar
```

<!--more-->

<br/>



#### 一般属性

1. `Manifest-Version` 

   用来定义`manifest文件`的版本，例如：`Manifest-Version: 1.0` 

2. `Created-By` 

      声明该文件的生成者，一般该属性是由jar命令行工具生成的，例如：`Created-By: Apache Ant 1.5.1` 

3. `Signature-Version` ：定义jar文件的签名版本

4. `Class-Path` ：应用程序或者类装载器使用该值来构建内部的类搜索路径

<br/>



#### 应用程序相关属性

1. `Main-Class` 

   定义`jar文件`的入口类，该类必须是一个可执行的类，一旦定义了该属性即可通过` java -jar x.jar`来运行该`jar文件`。

<br/>

