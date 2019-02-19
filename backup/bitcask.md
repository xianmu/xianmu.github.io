---
title: bitcask
tags: database
---

Bitcask是又一种存储引擎数据结构。与LSM-tree一样，bitcask转随机写为顺序写；与LSM-tree的有序结构不同，bitcask不对kv排序，使用hash表做索引加速read，当然也不能支持range query(scan)。
