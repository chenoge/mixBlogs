---
title: Spring-Autowired和Resource
date: 2019-06-05 16:20:59
tags: [Spring,Autowired,Resource]
---

##### @Resource和@Autowired

@Resource和@Autowired都是做bean的注入时使用，其实@Resource并不是Spring的注解，它的包是`javax.annotation.Resource`，需要导入，但是Spring支持该注解的注入。

<!--more-->

<br/>



#### @Autowired

@Autowired为Spring提供的注解，需要导入`org.springframework.beans.factory.annotation.Autowired`，只按照byType注入。

```java
public class TestServiceImpl {
    // 下面两种@Autowired只要使用一种即可
    @Autowired
    private UserDao userDao; // 用于字段上
    
    @Autowired(required = false)
    public void setUserDao(UserDao userDao) { // 用于属性的方法上
        this.userDao = userDao;
    }
}
```

@Autowired注解是按照类型（byType）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它的`required`属性为false。

<br/>



#### @Resource

@Resource默认按照ByName自动注入，由J2EE提供，需要导入包`javax.annotation.Resource`。@Resource有两个重要的属性：name和type。

如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不制定name也不制定type属性，这时将通过反射机制使用byName自动注入策略。

```java
public class TestServiceImpl {
    // 下面两种@Resource只要使用一种即可
    @Resource(name="userDao")
    private UserDao userDao; // 用于字段上
    
    @Resource(name="userDao")
    public void setUserDao(UserDao userDao) { // 用于属性的setter方法上
        this.userDao = userDao;
    }
}
```