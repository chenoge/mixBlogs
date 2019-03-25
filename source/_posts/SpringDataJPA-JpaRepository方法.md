---
title: SpringDataJPA-JpaRepository方法
date: 2019-03-19 21:05:25
tags: [JPA,Spring,Repository]
---

##### 解析方法名创建查询

```
find + 全局修饰 + By + 实体的属性名称 + 限定词 + 连接词
	 + …(其它实体属性) + OrderBy + 排序属性 + 排序方向
```

```java
// 分页查询出符合姓名的记录,同理Sort也可以直接加上
public List<User> findByName(String name, Pageable pageable);
```
<!--more-->

<br/>



```
全局修饰： Distinct， Top， First

关键词： IsNull， IsNotNull， Like， NotLike， Containing， In， NotIn，

IgnoreCase， Between， Equals， LessThan， GreaterThan， After， Before…

排序方向： Asc， Desc

连接词： And， Or

And — 等价于 SQL 中的 and 关键字，比如 findByUsernameAndPassword(String user, Striang pwd)；

Or — 等价于 SQL 中的 or 关键字，比如 findByUsernameOrAddress(String user, String addr)；

Between — 等价于 SQL 中的 between 关键字，比如 findBySalaryBetween(int max, int min)；

LessThan — 等价于 SQL 中的 “<”，比如 findBySalaryLessThan(int max)；

GreaterThan — 等价于 SQL 中的”>”，比如 findBySalaryGreaterThan(int min)；

IsNull — 等价于 SQL 中的 “is null”，比如 findByUsernameIsNull()；

IsNotNull — 等价于 SQL 中的 “is not null”，比如 findByUsernameIsNotNull()；

NotNull — 与 IsNotNull 等价；

Like — 等价于 SQL 中的 “like”，比如 findByUsernameLike(String user)；

NotLike — 等价于 SQL 中的 “not like”，比如 findByUsernameNotLike(String user)；

OrderBy — 等价于 SQL 中的 “order by”，比如 findByUsernameOrderBySalaryAsc(String user)；

Not — 等价于 SQL 中的 “！ =”，比如 findByUsernameNot(String user)；

In — 等价于 SQL 中的 “in”，比如 findByUsernameIn(Collection userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；

NotIn — 等价于 SQL 中的 “not in”，比如 findByUsernameNotIn(Collection userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；
```

