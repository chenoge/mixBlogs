---
title: Elasticsearch聚合排序
date: 2019-08-06 14:35:41
tags: [Elasticsearch,排序]
---

####  内置排序

聚合引入了一个 `order` 对象， 它允许我们可以根据以下几个值中的一个值进行排序：

- **`_count`** ：按文档数排序。对 `terms` 、 `histogram` 、 `date_histogram` 有效
- **`_term`** ： 按词项的字符串值的字母顺序排序。只在 `terms` 内使用
- **`_key`** ：按每个桶的键值数值排序。 只在 `histogram` 和 `date_histogram` 内使用

```javascript
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "colors" : {
            "terms" : {
              "field" : "color",
              "order": {
                "_count" : "desc" 
              }
            }
        }
    }
}
```

注：Elasticsearch默认的，桶会根据 `doc_count` 降序排列

<!--more-->

<br/>



####  按度量排序

**基于度量计算的结果值**进行排序

```javascript
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "colors" : {
            "terms" : {
              "field" : "color",
              "order": {
                "avg_price" : "desc" 
              }
            },
            // 度量计算
            "aggs": {
                "avg_price": {
                    "avg": {"field": "price"} 
                }
            }
        }
    }
}
```

如果想使用多值度量进行排序， 我们只需以关心的度量为关键词使用点式路径：

```javascript
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "colors" : {
            "terms" : {
              "field" : "color",
              "order": {
                // 根据方差排序
                "stats.variance" : "asc" 
              }
            },
            "aggs": {
                "stats": {
                    // 多值度量计算
                    "extended_stats": {"field": "price"}
                }
            }
        }
    }
}
```

<br/>



####  基于“深度”度量排序

在一定条件下，我们也有可能对**更深的度量**（孙子桶或从孙桶）进行排序。我们可以定义更深的路径，将度量用尖括号（ `>` ）嵌套起来，像这样： `my_bucket>another_bucket>metric` 。

```javascript
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "colors" : {
            "histogram" : {
              "field" : "price",
              "interval": 20000,
              "order": {
                "red_green_cars>stats.variance" : "asc" 
              }
            },
            "aggs": {
                "red_green_cars": {
                    "filter": { "terms": {"color": ["red", "green"]}}, 
                    "aggs": {
                        "stats": {"extended_stats": {"field" : "price"}} 
                    }
                }
            }
        }
    }
}
```

注：**嵌套路径上的每个桶都必须是单值的**

- `filter` 桶生成 一个单值桶：所有与过滤条件匹配的文档都在桶中
  - 目前，只有三个单值桶： `filter` 、 `global` 和 `reverse_nested` 

- 多值桶（如：`terms` ）动态生成许多桶，无法通过指定一个确定路径来识别

<br/>