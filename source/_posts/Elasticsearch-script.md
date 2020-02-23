---
title: Elasticsearch_script
date: 2020-02-23 10:18:43
tags: [Elasticsearch,script]
---

#### 脚本查询

无论Elasticsearch API支持何种脚本，语法都遵循相同的模式：

```
  "script": {
    "lang":   "...",  
    "source" | "id": "...", 
    "params": { ... } 
  }
```

- lang：脚本编写的语言，默认为`painless`
- ` source | id`：内联脚本或存储脚本的`id`
- params：应传递给脚本的任何命名参数

```json
GET my_index/_search
{
  "script_fields": {
    "my_doubled_field": {
      "script": {
        "lang":   "expression",
        "source": "doc['my_field'] * multiplier",
        "params": {
          "multiplier": 2
        }
      }
    }
  }
}
```

注：

- Elasticsearch第一次看到一个新脚本，它会编译它，并将编译后的版本存储在缓存中
- 如果需要将**变量**传递给脚本，则应将它们作为命名参数传递给脚本本身而不是硬编码值
- 如果你在很短的时间内编译了太多独特的脚本，Elasticsearch将使用`circuit_breaking_exception`错误拒绝新的动态脚本
- 默认情况下，每分钟将编译最多15个内联脚本



#### 存储的脚本

① 在集群状态下创建名为`calculate-score`的脚本

```json
POST _scripts/calculate-score
{
  "script": {
    "lang": "painless",
    "source": "Math.log(_score * 2) + params.my_modifier"
  }
}
```

② 可以使用以下命令检索相同的脚本：

```json
GET _scripts/calculate-score
```

③ 可以通过指定`id`参数来使用存储的脚本

```json
GET _search
{
  "query": {
    "script": {
      "script": {
        "id": "calculate-score",
        "params": {
          "my_modifier": 2
        }
      }
    }
  }
}
```



#### 脚本聚合

```json
POST es_stat_mth_chain_revenue_width/doc/_search
{
  "aggs": {
    "cityLevel": {
      "terms": {
        // text keyword
        "field": "city_level.keyword"
      },
      "aggs": {
        "room_count_sum": {
          "sum": {
            "field": "room_count"
          }
        },
        "status_room_count_sum": {
          "sum": {
            "field": "status_room_count"
          }
        },
        "occ": {
          "bucket_script": {
            "script": "params.B / params.A * 100",
            "buckets_path": {
              "A": "room_count_sum",
              "B": "status_room_count_sum"
            }
          }
        }
      }
    }
  }
}
```

