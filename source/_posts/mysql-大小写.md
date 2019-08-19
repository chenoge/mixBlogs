---
title: mysql-大小写
date: 2019-08-19 18:23:04
tags: [mysql,大小写]
---

#### 敏感性

在`MySQL`中，**数据库**在系统上是以一个**文件夹**的形式出现的，**表**对应着一个**文件**(也可能是多个，取决于存储引擎)。因此，**操作系统的大小写敏感性决定了数据库名和表名的大小写敏感性**。
    



在大多数`Unix`中**数据库名和表名**对大小写敏感，而在`Windows`中对大小写不敏感。一个显著的例外情况是`Mac OS X`，它基于`Unix`，但使用默认文件系统类型(`HFS+`)，对大小写不敏感。然而，`Mac OS X`也支持`UFS`卷，该卷对大小写敏感，就像`Unix`一样。

<!--more-->

<br/>



变量`lower_case_file_system`，说明**文件系统**对文件名的大小写是否敏感。`ON`说明对文件名的大小写不敏感，`OFF`表示敏感。`unix/linux`下`lower_case_table_names`默认值为 0 ，`Windows`下默认值是 1 ，`Mac OS X`下默认值是 2。

```
lower_case_table_names = 0  表名存储为给定的大小和比较是区分大小写的
lower_case_table_names = 1  表名存储在磁盘是小写的，但是比较的时候是不区分大小写
lower_case_table_names = 2  表名存储为给定的大小写但是比较的时候是小写的
```

```mysql
# 查看数据存储目录
show global variables like '%datadir%';

# 查看文件系统大小写敏感情况
show variables like 'lower%';
```

<br/>



#### 写区分规则

`linux`： 

- **数据库名**与**表名**是严格区分大小写的

- **表的别名**是严格区分大小写的

- **列名与列的别名**在所有的情况下均是忽略大小写的

- **变量名**也是严格区分大小写的

`windows`：

- 都不区分大小写
      

`Mac OS`下（非UFS卷）

- 都不区分大小写



