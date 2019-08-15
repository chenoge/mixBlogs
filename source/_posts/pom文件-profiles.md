---
title: pom文件-profiles
date: 2019-08-15 18:22:49
tags: [pom.xml,profiles,spring.profiles.active]
---

在Maven项目中，profile是根据不同的构件环境，对构建（build）过程进行动态配置的手段。可以通过`pom.xml`定义多个profile，也可以通过`settings.xml`文件定义多个profile。对于一个profile，如果同时在`pom.xml`和`settings.xml`中配置，则`settings.xml`中的**配置优先**。

<!--more-->

<br/>



`settings.xml`中的profile只能配置如下元素：

- `id`
- `activation`
- `repositories`
- `pluginRepositories`
- `properties`

<br/>



`pom.xml`中的profile能配置如下元素：

- `id`
- `activation`
- `repositories`
- `pluginRepositories`
- `properties`
- `build`
- `modules`
- `dependencies`
- `dependencyManagement`
- `repositories`
- `pluginRepositories`
- `distributionManagement`
- `reporting`

<br/>



```xml
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <conf.dir>profile/dev/</conf.dir>
        </properties>
        <activation>
            <!-- 启用spring.profiles.active配置 -->
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <profile>
        <id>prod</id>
        <properties>
            <conf.dir>profile/prod/</conf.dir>
        </properties>
    </profile>
</profiles>
```

```xml
<resources>
    <resource>
        <directory>${conf.dir}</directory>
        <filtering>true</filtering>
        <includes>
            <include>**/*.properties</include>
            <include>**/*.xml</include>
        </includes>
    </resource>
</resources>
```

<br/>



##### `spring.profiles.active`

`spring boot`允许你通过命名约定按照一定的格式(`application-{profile}.properties`)来定义多个配置文件，然后通过在`application.properyies`通过`spring.profiles.active`来具体激活一个或者多个配置文件。

如果没有没有指定任何`profile`的配置文件的话，`spring boot`默认会启动`application-default.properties`。

```
# application-{profile}.properties
# profile: docx...
spring.profiles.active=docx,es,redis,mail,restful,other,org
```



