---
title: Elasticsearch基本使用
date: 2019-03-11 18:44:39
tags: [Elasticsearch,ES]
---

#### 面向文档

通常，应用程序中的**对象拥有复杂的数据结构**，可能包括**日期**、**地理信息**、**其他对象**或者**数组**等。如果要把这些对象存储在**关系型数据库**中，相当于是把一个表现力丰富的对象挤压到一个非常大的电子表格中：你必须**将对象扁平化来适应表结构**，而且又不得不在每次查询时重新构造对象：

- 一个简单字段**对应一列** 
- 一个对象字段需要**新建表、引用主键** 
- 一个数组字段需要**新建表、引用主键、每个数组元素对应一行**  

`Elasticsearch`使用`JavaScript Object Notation`或者`JSON`作为文档的序列化格式。`JSON `序列化被大多数编程语言所支持，并且已经成为 `NoSQL `领域的标准格式。

注：文档 =》对象

<br/>

#### 请求格式

一个`Elasticsearch`请求和任何`HTTP`请求一样由若干相同的部件组成：

```
curl -X <VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

被 `< >` 标记的部件：

| 参数           | 说明                                                         |
| -------------- | :----------------------------------------------------------- |
| `VERB`         | 适当的 HTTP *方法* 或 *谓词* : `GET`、 `POST`、 `PUT`、 `HEAD` 或者 `DELETE` |
| `PROTOCOL`     | `http` 或者 `https`                                          |
| `HOST`         | Elasticsearch 集群中任意节点的主机名                         |
| `PORT`         | 运行 Elasticsearch HTTP 服务的端口号，默认是 `9200` 。       |
| `PATH`         | API 的终端路径，由`index、type、id` 等组成                   |
| `QUERY_STRING` | 任意可选的查询字符串参数，`?pretty` 将格式化地输出 JSON 返回值 |
| `BODY`         | 一个 JSON 格式的请求体 (如果请求需要的话)                    |

例如，计算集群中文档的数量，我们可以用这个：

```
curl -X GET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
```

<!--more-->

<br/>

#### 搜索格式

- 搜索所有`type`

  ```
  GET /megacorp/employee/_search
  ```

  注：使用 `_search `代替具体的`id` 

- 参数搜索

  ```
  # 查询字符串
  GET /megacorp/employee/_search?q=last_name:Smith
  ```

  ```
  # match 查询
  GET /megacorp/employee/_search
  {
      "query" : {
          "match" : {
              "last_name" : "Smith"
          }
      }
  }
  ```

  ```
  # filter 过滤器
  GET /megacorp/employee/_search
  {
      "query" : {
          "bool": {
              "must": {
                  "match" : {
                      "last_name" : "smith" 
                  }
              },
              "filter": {
                  "range" : {
                      "age" : { "gt" : 30 } 
                  }
              }
          }
      }
  }
  ```

  ```
  # match_phrase 查询
  GET /megacorp/employee/_search
  {
      "query" : {
          "match_phrase" : {
              "about" : "rock climbing"
          }
      }
  }
  ```

  

