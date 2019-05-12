---
title: redis-annotation-3
tags: database
---

继续阅读redis源码。前面看了基本数据结构，接下来看数据库相关实现。

# redisDb

数据结构：

```
// Redis database representation. There are multiple databases identified
// by integers from 0 (the default database) up to the max configured
// database. The database number is the 'id' field in the structure.
typedef struct redisDb {
    dict *dict;                 // key-value pairs
    dict *expires;              // Timeout of keys with a timeout set
    dict *blocking_keys;        // Keys with clients waiting for data (BLPOP)
    dict *ready_keys;           // Blocked keys that received a PUSH
    dict *watched_keys;         // WATCHED keys for MULTI/EXEC CAS 
    int id;                     // Database ID 
    long long avg_ttl;          // Average TTL, just for stats 
} redisDb;
```

# rdb

rdb文件格式：

 -----------------------------------------------
|REDIS|ver(4B)|aux|database|EOF(1B)|checksum(8B)|
 -----------------------------------------------

其中aux辅助信息：。。。

rdb如何处理事务数据？

# aof

# pub/sub

