---
title: Elasticsearch搜索
date: 2019-03-28 10:53:27
tags: [Elasticsearch,ES]
---

有两种形式的 `搜索 API`：一种是`轻量的查询字符串版本`，要求在查询字符串中传递所有的参数，另一种是更完整的` 请求体版本`，要求使用 JSON 格式和更丰富的查询表达式作为搜索语言。

#### 查询字符串

```
GET /_all/tweet/_search?q=+name:john+tweet:mary
# 查询在 name 字段中包含 john 并且在 tweet 字段中包含 mary 的文档
```

- `+` 前缀表示必须与查询条件匹配
-  `-` 前缀表示一定不与查询条件匹配
- 没有 `+` 或者 `-` 的所有其他条件都是可选的
- 匹配的越多，文档就越相关
- 取所有字段的值拼接成一个大的字符串，作为 `_all` 字段进行索引

```
+name:(mary john) +date:>2014-09-10 +(aggregations geo)
# name 字段中包含 mary 或者 john
# date 值大于 2014-09-10
# _all 字段包含 aggregations 或者 geo
```

注：查询字符串搜索允许任何用户在索引的**任意字段**上执行可能**较慢且重量级**的查询，这可能会暴露隐私信息，甚至将集群拖垮。

<!--more-->

<br/>



#### 请求体查询

请求体查询，不仅可以处理自身的查询请求，还允许你对结果进行片段强调（高亮）、对所有或部分结果进行聚合分析，同时还可以给出 你是不是想找的建议。

#####  match_all 查询

`match_all` 查询简单的 匹配所有文档。在没有指定查询方式时，它是默认的查询：

```js
{ "match_all": {}}
```

<br/>



#####  match 查询

无论你在任何字段上进行的是全文搜索还是精确查询，`match` 查询是你可用的标准查询。

如果你在一个全文字段上使用 `match` 查询，在执行查询前，它将用正确的分析器去分析查询字符串：

```js
{ "match": { "tweet": "About Search" }}
// 如果你在一个全文字段上使用 match 查询，在执行查询前，它将用正确的分析器去分析查询字符串
// 如果在一个精确值的字段上使用它， 例如数字、日期、布尔或者一个 not_analyzed 字符串字段
// 那么它将会精确匹配给定的值
```

<br/>



#####  multi_match 查询

`multi_match` 查询可以在多个字段上执行相同的 `match` 查询：

```js
{
    "multi_match": {
        "query":    "full text search",
        "fields":   [ "title", "body" ]
    }
}
```

<br/>



#####  range 查询

`range` 查询找出那些落在指定区间内的数字或者时间：

```js
{
    "range": {
        "age": {
            "gte":  20,
            "lt":   30
        }
    }
}
// gt 	大于
// gte 	大于等于
// lt 	小于
// lte 	小于等于
```

<br/>



#####  term 查询

`term` 查询被用于精确值匹配，这些精确值可能是数字、时间、布尔或者那些 `not_analyzed` 的字符串：

```js
{ "term": { "age":    26           }}
{ "term": { "date":   "2014-09-01" }}
{ "term": { "public": true         }}
{ "term": { "tag":    "full_text"  }}
```

<br/>



#####  terms 查询

`terms` 查询和 `term` 查询一样，但它允许你指定多值进行匹配。如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件：

```js
{ "terms": { "tag": [ "search", "full_text", "nosql" ] }}
```

<br/>



#####  exists 查询和 missing 查询

`exists` 查询和 `missing` 查询被用于查找那些指定字段中有值 (`exists`) 或无值 (`missing`) 的文档。这与SQL中的 `IS_NULL` (`missing`) 和 `NOT IS_NULL` (`exists`) 在本质上具有共性：

```js
{
    "exists":   {
        "field":    "title"
    }
}
```

<br/>



#####  组合多查询 `bool` 

 `bool` 查询将多个查询组合在一起，成为用户自己想要的布尔查询。它接收以下参数：

- `must`：文档 必须 匹配这些条件才能被包含进来
- `must_not`：文档 必须不匹配这些条件才能被包含进来
- `should`：如果满足这些语句中的任意语句，将增加 `_score` ，否则，无任何影响
- `filter`：必须匹配，但它以不评分、过滤模式来进行

一条复合语句可以合并 *任何* 其它查询语句，包括复合语句

```
{
    "query":{
        "match_phrase":{
            "about":"rock climbing"
        },
        "match":{
            "last_name":"Smith"
        },
        "bool":{
            "must":{
                "match":{
                    "tweet":"elasticsearch"
                }
            },
            "must_not":{
                "match":{
                    "name":"mary"
                }
            },
            "should":[
                {
                    "match":{
                        "starred":true
                    }
                },
                {
                    "bool":{
                        "must":{
                            "match":{
                                "folder":"inbox"
                            }
                        },
                        "must_not":{
                            "match":{
                                "spam":true
                            }
                        }
                    }
                }
            ],
            "filter":{
                "range":{
                    "age":{
                        "gt":30
                    }
                }
            }
        },
        "minimum_should_match":1
    },
    "highlight":{
        "fields":{
            "about":{

            }
        }
    },
    "aggs":{
        "all_interests":{
            "terms":{
                "field":"interests"
            },
            "aggs":{
                "avg_age":{
                    "avg":{
                        "field":"age"
                    }
                }
            }
        }
    }
}
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