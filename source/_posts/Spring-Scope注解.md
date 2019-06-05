---
title: Spring-Scope注解
date: 2019-06-05 17:39:07
tags: [Spring,Scope]
---

spring中scope是一个非常关键的概念，简单说就是对象在spring容器（IOC容器）中的生命周期，也可以理解为对象在spring容器中的创建方式。scope有5种取值：`singleton、prototype、request、session 和 global session`。

- `singleton`单例模式，全局有且仅有一个实例
- `prototype`原型模式，每次获取Bean的时候会有一个新的实例
- `request`表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效
- `session`表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效
- `global session`类似于标准的HTTP Session作用域