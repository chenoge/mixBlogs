---
title: Elasticsearch聚合
date: 2019-05-14 15:40:58
tags: [Elasticsearch]
---

Elasticsearch 有一个功能叫聚合（aggregations），允许我们基于数据生成一些精细的分析结果。聚合与 SQL 中的 `GROUP BY` 类似但更强大。

<!--more-->



- 普通嵌套

```javascript
GET /cars/transactions/_search
{
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
             // avg_price桶
            "avg_price": { 
               "avg": {
                  "field": "price"
               }
            },
            // 与avg_price桶并列
            "make": { 
                "terms": {
                    "field": "make" 
                }
            }
         }
      }
   }
}
```



- `nested`嵌套

```javascript
POST ds-epsm-all/post/_search
{
	"aggs": {
        // 情感
        "sentiment": {
            "nested": {
                "path": "obj_info"
            },
            "aggs": {
                // 情感-对象（专题）
                "name": {
                    "terms": {
                        "field": "obj_info.obj_name",
                        "size": 1
                    },
                    "aggs": {
                        // 情感-类型（正中负）
                        "sentiment": {
                            "terms": {
                                "field": "obj_info.obj_sentiment",
                                "size": 10
                            }
                        }
                    }
                }
            }
        }
    }
}
```



- 过滤功能

```javascript
GET /cars/transactions/_search
{
   "aggs":{
      "recent_sales": {
         "filter": { 
            "range": {
               "sold": {
                  "from": "now-1M"
               }
            }
         },
         "aggs": {
            "average_price":{
               "avg": {
                  "field": "price" 
               }
            }
         }
      }
   }
}
```



- 指定聚合值

```javascript
GET ds-epsm-all/post/_search
{
  "aggs": {
    "subject": {
      "terms": {
        "field": "category_tags",
        // 包括
        "include": ["母品牌", "伊利股份"],
        // 排除
        "exclude": ["蒙牛"],
      }
    }
  }
}
```



- `top_hits` 

```javascript
GET ds - epsm - all / post / _search 
{
    "aggs": {
        "similar": {
            "top_hits": {
                "size": 3,
                "sort": [
                    {
                        "similar_doc_count": {
                            "order": "desc"
                        }
                    }
                ]
            }
        }
    }
}
```



- 按时间统计

```javascript
GET /cars/transactions/_search
{
   "aggs": {
      "sales": {
         "date_histogram": {
            "field": "sold",
            "interval": "month", 
            "format": "yyyy-MM-dd" 
         }
      }
   }
}
```

