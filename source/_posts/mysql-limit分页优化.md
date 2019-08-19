---
title: mysql-limit分页优化
date: 2019-08-19 15:07:53
tags: [mysql,limit]
---

#### limit

一般，我们在做分页时，用的语句如下：

```mysql
select * from product limit 400000, 20;  # 3.229秒
```

但是，如果数据量很大，则利用以上的查询会非常慢：

```mysql
select * from product limit 866613, 20;  # 37.44秒
```

<!--more-->

<br/>



#### 优化

利用了索引查询的语句中`如果只包含了那个索引列`（**覆盖索引**），那么这种情况会查询很快。`id字段`是主键，自然就包含了默认的**主键索引**。现在让我们看看**利用覆盖索引的查询**效果如何：

```mysql
select id from product limit 866613, 20;  # 0.2秒
```

那么如果我们也要**查询所有列**，有两种方法：

- `id>=`的形式：

  ```mysql
  SELECT * FROM product 
  WHERE ID > =(select id from product limit 866613, 1) limit 20;  # 0.2秒
  ```

- 利用`join`

  ```mysql
  SELECT * FROM product a 
  JOIN (select id from product limit 866613, 20) b ON a.ID = b.id;
  ```

