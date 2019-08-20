---
title: mysql-explain
date: 2019-08-20 17:07:24
tags: [mysql,explain]
---

#### EXPLAIN 命令

`MySQL` 提供了一个 `EXPLAIN` 命令，它可以对 `SELECT` 语句进行分析，并输出 `SELECT` 执行的详细信息，以供开发人员针对性优化。

<!--more-->

<br/>



#### 创建表

```mysql
CREATE TABLE `user_info` (
  `id`   BIGINT(20)  NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) NOT NULL DEFAULT '',
  `age`  INT(11)              DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `name_index` (`name`)
)
  ENGINE = InnoDB
  DEFAULT CHARSET = utf8
```

```mysql
CREATE TABLE `order_info` (
  `id`           BIGINT(20)  NOT NULL AUTO_INCREMENT,
  `user_id`      BIGINT(20)           DEFAULT NULL,
  `product_name` VARCHAR(50) NOT NULL DEFAULT '',
  `productor`    VARCHAR(30)          DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `user_product_detail_index` (`user_id`, `product_name`, `productor`)
)
```

<br/>



#### 输出格式

```
mysql> explain select * from user_info where id = 2;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: user_info
   partitions: NULL
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 8
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)
```

- `id`: `SELECT` 查询的标识符，每个 `SELECT` 都会自动分配一个唯一的标识符
  - 在嵌套查询中，`id`越大的语句，越先执行
- `select_type`: SELECT 查询的类型
- `table`: 查询的是哪个表
- `partitions`: 匹配的分区
- `type`: join 类型
- `possible_keys`: 此次查询中可能选用的索引
- `key`: 此次查询中确切使用到的索引
- `ref`: 哪个字段或常数与 key 一起被使用
- `rows`: 显示此查询一共扫描了多少行，这个是一个估计值
- `filtered`: 表示此查询条件所过滤的数据的百分比
- `extra`: 额外的信息

<br/>



#### select_type

- `SIMPLE`, 表示此查询不包含 UNION 查询或子查询
- `PRIMARY`, 表示此查询是**最外层**的查询
- `UNION`, 表示此查询是 `UNION` 的第二或随后的查询
- `DEPENDENT UNION`, UNION 中的第二个或后面的查询语句，取决于外面的查询
- `UNION RESULT`, UNION 的结果
- `SUBQUERY`, 子查询中的第一个 SELECT
- `DEPENDENT SUBQUERY`: 子查询中的第一个 SELECT，取决于外面的查询，即子查询依赖于外层查询的结果

<br/>



#### type

`type` 字段比较重要，它提供了判断查询是否高效的重要依据依据。通过 `type` 字段，我们判断此次查询是 `全表扫描` 还是 `索引扫描` 等。

常用的类型有： `ALL, index,  range, ref, eq_ref, const, system, NULL`（从左到右，性能从差到好）

- `system`: 表中只有一条数据，这个类型是特殊的 `const` 类型

- `const`: 针对主键或唯一索引的等值查询扫描, 最多只返回一行数据

- `eq_ref`: 此类型通常出现在多表的 join 查询，表示对于前表的每一个结果，都只能匹配到后表的一行结果。并且查询的比较操作通常是 `=`，查询效率较高

  ```
  mysql> EXPLAIN SELECT * FROM user_info, order_info WHERE user_info.id = order_info.user_id
  *************************** 1. row ***************************
             id: 1
    select_type: SIMPLE
          table: order_info
     partitions: NULL
           type: index
  possible_keys: user_product_detail_index
            key: user_product_detail_index
        key_len: 314
            ref: NULL
           rows: 9
       filtered: 100.00
          Extra: Using where; Using index
  *************************** 2. row ***************************
             id: 1
    select_type: SIMPLE
          table: user_info
     partitions: NULL
           type: eq_ref
  possible_keys: PRIMARY
            key: PRIMARY
        key_len: 8
            ref: test.order_info.user_id
           rows: 1
       filtered: 100.00
          Extra: NULL
  2 rows in set, 1 warning (0.00 sec)
  ```

- `ref`: 此类型通常出现在多表的 join 查询，针对于非唯一或非主键索引，或者是使用了 `最左前缀` 规则索引的查询

  ```
  mysql> EXPLAIN SELECT * FROM user_info, order_info WHERE user_info.id = order_info.user_id AND order_info.user_id = 5
  *************************** 1. row ***************************
             id: 1
    select_type: SIMPLE
          table: user_info
     partitions: NULL
           type: const
  possible_keys: PRIMARY
            key: PRIMARY
        key_len: 8
            ref: const
           rows: 1
       filtered: 100.00
          Extra: NULL
  *************************** 2. row ***************************
             id: 1
    select_type: SIMPLE
          table: order_info
     partitions: NULL
           type: ref
  possible_keys: user_product_detail_index
            key: user_product_detail_index
        key_len: 9
            ref: const
           rows: 1
       filtered: 100.00
          Extra: Using index
  2 rows in set, 1 warning (0.01 sec)
  ```

- `range`: 表示使用索引范围查询，通过索引字段范围获取表中部分数据记录。这个类型通常出现在 `=, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, IN() `操作中

  - 当 `type` 是 `range` 时， `ref` 字段为 `NULL`, 并且 `key_len` 字段是此次查询中使用到的索引的最长的那个

- `index`: 表示全索引扫描(`full index scan`)，和 ALL 类型类似，只不过 ALL 类型是全表扫描，而 index 类型则**仅仅扫描所有的索引**，而不扫描数据

- `ALL`：`Full Table Scan`， MySQL将遍历全表以找到匹配的行

<br/>



#### key_len

表示查询优化器使用了索引的字节数，这个字段可以评估组合索引是否完全被使用，或只有最左部分字段被使用到。`key_len`的计算规则如下：

- 字符串

  - `char(n)`: n 字节长度
  - `varchar(n)`: 如果是 `utf8` 编码, 则是 3 *n + 2字节；如果是 utf8mb4 编码, 则是 4* n + 2 字节

- 数值类型:

  - `TINYINT`: 1字节
  - `SMALLINT`: 2字节
  - `MEDIUMINT`: 3字节
  - `INT`: 4字节
  - `BIGINT`: 8字节

- 时间类型

  - `DATE`: 3字节
  - `TIMESTAMP`: 4字节
  - `DATETIME`: 8字节

- 字段属性: `NULL` 属性 占用一个字节， 如果一个字段是 `NOT NULL` 的，则没有此属性

  ```
  mysql> EXPLAIN SELECT * FROM order_info WHERE user_id = 1 AND product_name = 'p1';
  *************************** 1. row ***************************
             id: 1
    select_type: SIMPLE
          table: order_info
     partitions: NULL
           type: ref
  possible_keys: user_product_detail_index
            key: user_product_detail_index
        key_len: 161
            ref: const,const
           rows: 2
       filtered: 100.00
          Extra: Using index
  1 row in set, 1 warning (0.00 sec)
  ```

  我们的查询条件 `WHERE user_id = 1 AND product_name = 'p1'` 中，仅仅使用到了联合索引中的前两个字段。因此 `keyLen(user_id) + keyLen(product_name) = 9 + 50 * 3 + 2 = 161`。

  - `user_id` 字段是 `BIGINT`，占用 8 字节， 而 `NULL` 属性占用一个字节，总共是 9 个字节

<br/>



#### rows

rows 也是一个重要的字段。MySQL 查询优化器根据统计信息，估算 SQL 要查找到结果集需要扫描读取的数据行数。
这个值非常直观显示 SQL 的效率好坏，原则上 rows 越少越好。

<br/>





