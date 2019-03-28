---
title: Elasticsearch字段
date: 2019-03-28 10:53:27
tags: [Elasticsearch,ES]
---

#### 辅助字段

```
_index：文档在哪存放
_type：档表示的对象类别
_id：文档唯一标识

_cluster：集群
_shards：分片
_aliases：别名
_mapping
_source

_search：搜索
_count：总数
_score
_all
_create

?q=
?_source=
?pretty
?op_type=
```

<!--more-->

```
GET /_cluster/health
GET /_search?q=mary
GET /_all/tweet/_search?q=tweet:elasticsearch
GET /_search?q=%2Bname%3Ajohn+%2Btweet%3Amary
```

<br/>



#### 多索引、多类型

- `/_search`：在所有的索引中搜索所有的类型
- `/gb/_search`：在 gb 索引中搜索所有的类型
- `/gb,us/_search`：在 gb 和 us 索引中搜索所有的文档
- `/g*,u*/_search`：在任何以 g 或者 u 开头的索引中搜索所有的类型
- `/gb/user/_search`：在 gb 索引中搜索 user 类型
- `/gb,us/user,tweet/_search`：在 gb 和 us 索引中搜索 user 和 tweet 类型
- `/_all/user,tweet/_search`：在所有的索引中搜索 user 和 tweet 类型

<br/>