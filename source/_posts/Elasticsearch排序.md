---
title: Elasticsearch排序
date: 2019-05-14 15:26:16
tags: [Elasticsearch]
---

在 Elasticsearch 中， *相关性得分* 由一个浮点数进行表示，并在搜索结果中通过 `_score` 参数返回， 默认排序是 `_score` 降序。



- 简单指定字段排序

  ```
  "sort": "number_of_children"
  ```

  字段将会默认升序排序 ，而按照 `_score` 的值进行降序排序



- 按照字段的值排序

  ```
  GET /_search
  {
      "query" : {
          "bool" : {
              "filter" : { "term" : { "user_id" : 1 }}
          }
      },
      "sort": { "date": { "order": "desc" }}
  }
  ```

<!--more-->



- 多级排序

  ```
  GET /_search
  {
      "query" : {
          "bool" : {
              "must":   { "match": { "tweet": "manage text search" }},
              "filter" : { "term" : { "user_id" : 2 }}
          }
      },
      "sort": [
          { "date":   { "order": "desc" }},
          { "_score": { "order": "desc" }}
      ]
  }
  ```

  排序条件的顺序是很重要的。结果首先按第一个条件排序，仅当结果集的第一个 `sort` 值完全相同时才会按照第二个条件进行排序，以此类推。



- 多值（数组）字段排序

  ```
  "sort": {
      "dates": {
          "order": "asc",
          "mode":  "min"
      }
  }
  ```

  字段有多个值的排序，这些值并没有固有的顺序。对于数字或日期，你可以将多值字段减为单值，这可以通过使用 `min` 、 `max` 、 `avg` 或是 `sum` 排序模式。