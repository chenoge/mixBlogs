---
title: maven-pom配置
date: 2019-03-15 12:04:24
tags: [maven,pom]
---

`POM`是项目对象模型(`Project Object Model`)的简称,它是`Maven项目`中的文件。

<br/>



#### maven的协作相关属性

一个`pom.xml`的定义必须包含`modelVersion`、`groupId`、`artifactId`和`version`这四个元素，当然这其中的元素也是可以从它的父项目中继承的。使用groupdId:artifactId:version`的形式来唯一确定一个项目。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0  
                      http://maven.apache.org/maven-v4_0_0.xsd">
    
    <modelVersion>4.0.0</modelVersion>
    
    <!-- 
        含义：组织标识，定义了项目属于哪个组
        用途：此名称则是本地仓库中的路径
        例如：otowa.user.dao，在M2_REPO目录下，将是: otowa/user/dao目录
        命名规范:项目名称，模块，子模块
    -->
    <groupId>otowa.user.dao</groupId>
    
    <!-- 
        含义：项目名称，定义当前Maven项目在组中唯一的ID
        用途：例如：user-dao，在M2_REPO目录下，将是：otowa/user/dao/user-dao目录
        命名规范:唯一就好
    -->
    <artifactId>user-dao</artifactId>
    
    <!-- 
        含义：项目当前的版本号
        用途：例如：0.0.1-SNAPSHOT
		在M2_REPO目录下，将是：otowa/user/dao/user-dao/0.0.1-SNAPSHOT目录
    -->
    <version>0.0.1-SNAPSHOT</version>
    
    <!-- 打包的格式，可以为：pom , jar , maven-plugin , ejb , war , ear , rar , par -->
    <packaging>war</packaging>
    
    <!-- 元素声明了一个对用户更为友好的项目名称 -->
    <name>maven</name>
</project>
```

<br/>



#### 引入依赖

```xml
    <!-- 定义本项目的依赖关系 -->  
    <dependencies>  
  
        <!-- 每个dependency都对应这一个jar包 -->  
        <dependency>  
            <groupId>com.winner.trade</groupId>  
            <artifactId>trade-test</artifactId>  
            <version>1.0.0-SNAPSHOT</version>  
  
            <!-- 
				maven认为，程序对外部的依赖会随着程序的所处阶段和应用场景而变化
				所以maven中的依赖关系有作用域(scope)的限制。 
			-->  
            
            <!--
				scope包含如下的取值：compile（编译范围）、provided（已提供范围）
				runtime（运行时范围）、test（测试范围）、system（系统范围） 
			-->  
            <scope>test</scope>  
  
            <!-- 
				设置指依赖是否可选，默认为false,即子项目默认都继承:为true
				则子项目必需显示的引入，与dependencyManagement里定义的依赖类似  
			-->
            <optional>false</optional>  
  
            <!-- 
				屏蔽依赖关系。 比如项目中使用的libA依赖某个库的1.0版，
				libB依赖某个库的2.0版，现在想统一使用2.0版，就应该屏蔽掉对1.0版的依赖 
			-->
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                </exclusion>
            </exclusions>
  
        </dependency>
  
    </dependencies>
```

<br/>



#### 定义常量

```xml
<!-- 为pom定义一些常量，在pom中的其它地方可以直接引用 使用方式 如下 ：${file.encoding} -->  
<properties>
    <file.encoding>UTF-8</file.encoding>
    <java.source.version>1.5</java.source.version>
    <java.target.version>1.5</java.target.version>
</properties>
```

<br/>



#### 多环境配置

```xml
<profiles>
    <!-- 开发环境 -->  
    <profile>
        <id>dev</id>
        <properties>
            <conf.dir>profile/dev/</conf.dir>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <!-- 生成环境 -->  
    <profile>
        <id>prod</id>
        <properties>
            <conf.dir>profile/prod/</conf.dir>
        </properties>
    </profile>
</profiles>
```

<br/>