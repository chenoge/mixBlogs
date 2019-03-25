---
title: maven配置
date: 2019-03-15 12:19:40
tags: [maven]
---

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

| 目录                               | 目的                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| ${basedir}                         | **存放pom.xml和所有的子目录**                                |
| ${basedir}/src/main/java           | **项目的java源代码 **                                        |
| ${basedir}/src/main/resources      | **项目的资源，比如说property文件，springmvc.xml**            |
| ${basedir}/src/test/java           | 项目的测试类，比如说Junit代码                                |
| ${basedir}/src/test/resources      | 测试用的资源                                                 |
| ${basedir}/src/main/webapp/WEB-INF | **web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面** |
| ${basedir}/target                  | **打包输出目录**                                             |
| ${basedir}/target/classes          | 编译输出目录                                                 |
| ${basedir}/target/test-classes     | 测试编译输出目录                                             |
| Test.java                          | Maven只会自动运行符合该命名规则的测试类                      |
| ~/.m2/repository                   | **Maven默认的本地仓库目录位置**                              |

<br/>

<!--more-->



#### 修改默认的本地仓库

Maven 本地仓库默认被创建在 `%USER_HOME% `目录下。要修改默认位置，在 `%M2_HOME%\conf `目录中的 Maven 的 `settings.xml `文件中定义另一个路径

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository>C:/MyLocalRepository</localRepository>
</settings>
```

<br/>