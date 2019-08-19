---
title: mysql索引
date: 2019-06-29 20:50:10
tags: [sql,索引]
---

##### 一、原理

索引一般以**文件形式**存在磁盘中（也可以存于内存中），存储的索引的原理大致概括为**以空间换时间**。

数据库在**未添加索引**的时候进行查询默认的是进行**全量搜索**，也就是进行全局扫描，有多少条数据就要进行多少次查询，然后找到相匹配的数据就把他放到**结果集**中，直到全表扫描完。

**建立索引之后**，会将建立索引的`KEY值`放在一个**n叉树**上（`BTree`）。因为B树的特点就是适合在磁盘等直接存储设备上组织动态查找表，每次以索引进行条件查询时，会去树上根据`key值`直接进行搜索。

<!--more-->



##### 二、优点

```
建立索引的目的是加快对表中记录的查找或排序！
```

① 建立索引的列可以保证行的唯一性，生成唯一的`rowId`

② 建立索引可以有效缩短数据的检索时间

③ 建立索引可以加快表与表之间的连接

④ 为用来排序或者是分组的字段添加索引可以加快分组和排序顺序

<br/>



##### 三、缺点

① 创建索引和维护索引需要时间成本，这个成本随着数据量的增加而加大

② 创建索引和维护索引需要空间成本，每一条索引都要占据数据库的物理存储空间

③ 会降低表的增删改的效率，因为每次增删改索引需要进行动态维护，导致时间变长

<br/>



##### 四、创建索引

```sql
CREATE TABLE table_name [col_name data type] [unique | fulltext] 
[index | key] [index_name] (col_name[length]) [asc | desc]
```

- `unique | fulltext`为可选参数，分别表示唯一索引、全文索引
- `index`和`key`为同义词，两者作用相同，用来指定创建索引
- `col_name`为需要创建索引的字段列，该列必须从数据表中该定义的多个列中选择
- `index_name`指定索引的名称，为可选参数，如果不指定，默认`col_name`为索引值
- `length`为可选参数，表示索引的长度，只有字符串类型的字段才能指定索引长度
- `asc`或`desc`指定升序或降序的索引值存储



##### 直接创建索引

```sql
CREATE INDEX index_name ON table_name(col_name);
```



##### 修改表结构的方式添加索引

```sql
ALTER TABLE table_name ADD INDEX index_name(col_name);
```



##### 创建表的时候同时创建索引

```sql
CREATE TABLE `news` (
    `id` int(11) NOT NULL AUTO_INCREMENT ,
    `title` varchar(255)  NOT NULL ,
    `content` varchar(255)  NULL ,
    `time` varchar(20) NULL DEFAULT NULL ,
    PRIMARY KEY (`id`),
    INDEX index_name (title(255))
)
```



##### 删除索引

```sql
DROP INDEX index_name ON table_name;

alter table `表名` drop index 索引名;
```

