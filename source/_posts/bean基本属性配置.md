---
title: bean基本属性配置
date: 2019-03-19 18:25:09
tags: [spring,bean]
---
####` <beans>`标签

```xml
<beans xmlns="http://www.springframework.org/schema/beans" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="http://www.springframework.org/schema/beans  
           http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

- ##### `xmls`

`xmlns`是`XMLnameSpace`的缩写，`xml`的**标签名称**是自定义的，可能会和其他人的标签重名，而功能却不一样，所以需要加一个`namespace`来区分，类似`Java`中`package` 。

- ##### `xmlns:xsi`

是指`xml文件`遵守`xml规范`，`xsi`全名：`xml schema instance` ，是指具体用到`schema`资源文件里定义的规范所遵守的规范。即`/spring-beans-2.0.xsd`这个文件里定义的元素遵守什么标准

- ##### `xsi:schemaLaction`

`schemalLaction`属性来引用`（schema）`模式文档，解析器可以在需要的情况下使用这个文档对xml实例文档进行校验。它的值（URL）是成对出现的，第一个值表示命名空间，第二个值则表示描述该命名空间的模式文档的具体位置，两个值之间以空格分隔。

<!--more-->

<br/>



#### `<bean>`标签

```xml
<bean id="logDao" name="logDaoName" class="com.baobaotao.anno.LogDao" />

<bean id="userDao" class="com.baobaotao.anno.UserDao" />

<bean class="com.baobaotao.anno.LogonService">
    <constructor-arg ref="logDao"></constructor-arg>
    <constructor-arg ref="userDao"></constructor-arg>
</bean>

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="user" value="root"></property>
    <property name="password" value="123"></property>
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"></property>
</bean>
```

- `id`是`bean`的标识符，必须唯一。如果没有配置`id`，`name`默认为标识符

- 如果配置了`id`和`name`，那么`name`为别名。

- `name`可以设置多个别名，分隔符可以是`空格、逗号、分号`

- `class`是`bean`的**全限定名**，即`包名+类名`。如果不配置`id`和`name`，那么可以根据`applicationContext.getbean(Class)`获取对象

- `autowire` 自动装配 用于简化`spring`的配置

  ```
  byname: 根据名称去查找相应的bean,发现了则装载上
  bytype: 根据类型自动装配,不用去管id,但同一种类型的bean只能有一个,否则报错
  constructor：当通过构造器注入实例化bean时,装配构造方法
  ```

- `scope`作用域

  ```
  singleton: 单例的,整个容器只产生一个对象,默认是单例
  prototype: 原型,每次获取bean都创建一个新对象
  request: 每次请求时创建一个新的对象
  session: 在一个会话范围内只产生一个对象
  application: 在应用范围内是一个对象
  ```

<br/>



#### 分层次

程序严格来讲分为四个层次：`显示层（VO类）、控制层（Controller类）、业务层（Service类）、数据层（表：Entity类、CURD：DAO类）` 

<br/>

