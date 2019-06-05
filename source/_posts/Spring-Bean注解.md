---
title: Spring-Bean注解
date: 2019-06-05 16:38:48
tags: [Spring,Bean]
---

#### 管理Bean

Spring帮助我们管理Bean分为两个部分，**一个是注册Bean，一个装配Bean**。 
完成这两个动作有三种方式，一种是使用自动配置的方式、一种是使用JavaConfig的方式，一种就是使用XML配置的方式。

<br/>



#### @Bean 的用法 - JavaConfig

@Bean是一个方法级别上的注解，主要用在@Configuration注解的类里，也可以用在@Component注解的类里。

下面是@Configuration里的一个例子

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
}
```

这个配置就等同于之前在xml里的配置

```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

<!--more-->

<br/>



#### 接受生命周期的回调

任何使用@Bean定义的bean，也可以执行生命周期的回调函数，类似@PostConstructand @PreDestroy的方法。用法如下

```java
public class Foo {
    public void init() {
        // initialization logic
    }
}

public class Bar {
    public void cleanup() {
        // destruction logic
    }
}

@Configuration
public class AppConfig {
    @Bean(initMethod = "init")
    public Foo foo() {
        return new Foo();
    }

    @Bean(destroyMethod = "cleanup")
    public Bar bar() {
        return new Bar();
    }
}
```

默认使用javaConfig配置的bean，如果存在close或者shutdown方法，则在bean销毁时会自动执行该方法，如果你不想执行该方法，则添加@Bean(destroyMethod="")来防止出发销毁方法

<br/>



#### 指定bean的scope

你能够使用@Scope注解来指定使用@Bean定义的bean

```java
@Configuration
public class MyConfiguration {
    @Bean
    @Scope("prototype")
    public Encryptor encryptor() {
        // ...
    }
}
```

<br/>



#### 自定义bean的命名

默认情况下bean的名称和方法名称相同，你也可以使用name属性来指定

```java
@Configuration
public class AppConfig {
    @Bean(name = "myFoo")
    public Foo foo() {
        return new Foo();
    }
}

@Configuration
public class AppConfig {
    // 别名
    @Bean(name = { "dataSource", "subsystemA-dataSource", "subsystemB-dataSource" })
    public DataSource dataSource() {
        // instantiate, configure and return DataSource bean...
    }
}
```

<br/>



#### @component - 自动配置

`@Component`（和`@Service`和`@Repository`）用于自动检测和使用类路径扫描自动配置bean。注释类和bean之间存在隐式的一对一映射（即每个类一个bean）。这种方法对需要进行逻辑处理的控制非常有限，因为它纯粹是声明性的。

```java
@Component
public class Student {

    private String name = "lkm";

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

注：@component相当于配置文件中的`<bean id="" class=""/>`。

<br/>



@Component、@Bean都可以通过@Resource和@Autowired**装配**。如果你想要将第三方库中的组件装配到你的应用中，在这种情况下，是没有办法在它的类上添加@Component注解的，因此就不能使用自动化装配的方案了，但是我们可以使用@Bean，当然也可以使用XML配置。

