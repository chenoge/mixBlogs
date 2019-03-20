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
<!-- 空参构造创建 -->
<bean id="logDao" name="logDaoName" class="com.sjtu.bean.LogDao" />


<bean id="dataSource" class="com.sjtu.bean.dataSource">
    <!-- 值类型注入：为dataSource对象中名为user的属性注入root作为值 -->
    <property name="user" value="root"></property>
</bean>


<bean class="com.sjtu.bean..LogonService">
    <!-- 引用类型注入：为logDao属性注入logDao对象 -->
    <property name="logDao" ref="logDao"></property>
</bean>


 <!-- 准备带有参数的构造 -->
<bean name="user" class="com.sjtu.bean.User">
    <!-- name属性：构造函数参数名 -->
    <!-- index属性：构造函数参数索引  -->
    <!-- type属性：构造函数参数类型 -->
    <!-- 上述三个属性不必全部出现，根据情况选择即可 -->
    <constructor-arg name="name"
                     value="Jerry"
                     index="0"
                     type="java.lang.String"></constructor-arg>
    <constructor-arg name="car" ref="car"></constructor-arg>
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

程序严格来讲分为四个层次：

- `显示层：VO类`
- `控制层：Controller类`

- `业务层：Service类`

- `数据层：`
  - `表：Entity类 || PO类`
  - `CURD：DAO类）` 

<br/>

