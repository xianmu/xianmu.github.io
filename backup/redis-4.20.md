---
title: redis
tags: database
---

阅读了一遍redis-4.0的源码，这里写一个自己的总结。

# structure

redis-4.0代码目录结构如图：

# data structures

首先是数据结构部分，包括动态字符串sds, 双向链表adlist, dict, zskiplist以及做基数估计的hyperloglog等等。都是标准实现，熟悉基础数据结构的话，很快就能看完，难度不大。唯一陌生的可能是hyperloglog，翻阅相关文献，也不难看懂。

# utils

一些通用组件封装，包括zmalloc, anet, 。
