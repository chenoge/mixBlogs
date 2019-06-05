---
title: Java-PostConstruct和PreConstruct
date: 2019-06-05 17:52:51
tags: [Java,PostConstruct,PreConstruct]
---

从Java EE5规范开始，Servlet增加了两个影响Servlet生命周期的注解（Annotation）：@PostConstruct和@PreConstruct。这两个注解被用来修饰一个**非静态的void()方法**，而且这个方法不能有抛出异常声明。

```java
@PostConstruct
public void someMethod(){
    ...
}
```

- 被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器调用一次，类似于Serclet的inti()方法。被@PostConstruct修饰的方法会在**构造函数之后，init()方法之前**运行。
- 被@PreDestroy修饰的方法会在服务器卸载Servlet的时候运行，并且只会被服务器调用一次，类似于Servlet的destroy()方法。被@PreDestroy修饰的方法会在**destroy()方法之后运行，在Servlet被彻底卸载之前**。

<br/>