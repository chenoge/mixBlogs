---
title: JedisPool配置
date: 2019-08-01 12:05:13
tags: [redis,jedis,JedisPool]
---

##### redis配置文件

```
# REDIS (RedisProperties)

# Redis数据库索引（默认为0）
spring.redis.database=0

# Redis服务器地址
spring.redis.host=192.168.1.80

# Redis服务器连接端口
spring.redis.port=6379

# Redis服务器连接密码（默认为空）
spring.redis.password=redis

# 连接池最大连接数（默认为8，使用负值表示没有限制）
spring.redis.pool.max-active=8

# 连接池中的最大空闲连接（默认为8）
spring.redis.pool.max-idle=8

# 连接池中的最小空闲连接（默认为0）
spring.redis.pool.min-idle=0

# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1

# 连接超时时间，单位是毫秒（默认为2000ms）
spring.redis.timeout=2000
```

