---
title: mysql-left-join-on多条件无效
date: 2019-08-21 18:41:07
tags: [mysql,left join on]
---

#### 连表

数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的**临时表**，然后再将这张临时表返回给用户。在使用`left jion`时，`on`和`where`条件的区别如下：

- `on`条件是在生成临时表时使用的条件，它不管`on`中的**条件是否为真**，都会返回左边表中的记录

- `where`条件是在临时表生成好后，再对临时表进行过滤的条件

  ```mysql
  select * from tab1 left join tab2 on (tab1.size = tab2.size) where tab2.name='AAA';
  select * from tab1 left join tab2 on (tab1.size = tab2.size and tab2.name='AAA');
  ```