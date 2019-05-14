---
title: Elasticsearch字段类型
date: 2019-05-14 15:06:06
tags: [Elasticsearch,字段类型,映射]
---

#### 字段类型

- 简单域类型：
  - 字符串: `string`
  - 整数 : `byte`, `short`, `integer`, `long`
  - 浮点数: `float`, `double`
  - 布尔型: `boolean`
  - 日期: `date`

<!--more-->

<br/>



- 对象类型

```
{
    "tweet":            "Elasticsearch is very flexible",
    "user": {
        "id":           "@johnsmith",
        "gender":       "male",
        "age":          26,
        "name": {
            "full":     "John Smith",
            "first":    "John",
            "last":     "Smith"
        }
    }
}
```

```
{
  "gb": {
    "tweet": { 
      "properties": {
        "tweet":            { "type": "string" },
        "user": { 
          "type":             "object",
          "properties": {
            "id":           { "type": "string" },
            "gender":       { "type": "string" },
            "age":          { "type": "long"   },
            "name":   { 
              "type":         "object",
              "properties": {
                "full":     { "type": "string" },
                "first":    { "type": "string" },
                "last":     { "type": "string" }
              }
            }
          }
        }
      }
    }
  }
}
```

<br/>



- 数组类型

  在ElasticSearch中，没有专门的数组（Array）数据类型。但是，在默认情况下，任意一个字段都可以包含**0或多个值**，这意味着每个字段默认都是数组类型。不过，数组类型的**各个元素值的数据类型必须相同**。

  在ElasticSearch中，数组是开箱即用的（out of box），不需要进行任何配置，就可以直接使用。

<br/>



- 详细类型

![](https://chenoge.github.io/endBlogs/2019/05/14/Elasticsearch字段类型/字段类型表.png)

<br/>

