---
title: leveldb
tags: database
---

阅读了一遍leveldb-1.20的源码，这里写一个自己的总结，本文不解释lsm-tree等基础知识。

# structure

首先是代码结构：

# util

util目录下是一些通用组件，包括arena, bloom, cache, coding, comparator, crc32, hash, histogram, logging, mutex, random, status, testharness, testutil等。

# port

port目录下是一些跨平台特性的封装，包括atomic_pointer(memory barrier因平台而异), mutex/condition variable, 大小端等等。

# table

# db

db目录下内容比较多。skiplist可以算做通用组件。
