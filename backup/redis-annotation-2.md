---
title: redis-annotation-2
tags: database
---

继续阅读redis源码。

# quick list
quicklist在redis也是一个双向链表的实现（同样没有头结点且非循环），但其每个节点是一个ziplist。数据结构如下：

```
typedef struct quicklistNode {
    struct quicklistNode *prev;
    struct quicklistNode *next;
    unsigned char *zl;     // ziplist (maybe compressed)
    unsigned int sz;             // ziplist size in bytes 
    unsigned int count : 16;     // count of items in ziplist
    unsigned int encoding : 2;   // RAW==1 or LZF==2 
    unsigned int container : 2;  // NONE==1 or ZIPLIST==2 
    unsigned int recompress : 1; // was this node previous compressed? 
    unsigned int attempted_compress : 1; // node can't compress; too small 
    unsigned int extra : 10; // more bits to steal for future usage
} quicklistNode;

typedef struct quicklist {
    quicklistNode *head;
    quicklistNode *tail;
    unsigned long count;        // total count of all entries in all ziplists
    unsigned long len;          // number of quicklistNodes
    int fill : 16;              // fill factor for individual nodes
    unsigned int compress : 16; // depth of end nodes not to compress;0=off
} quicklist;

typedef struct quicklistIter {
    const quicklist *quicklist;
    quicklistNode *current;
    unsigned char *zi;
    long offset; // offset in current ziplist
    int direction;
} quicklistIter;
```

# redis object

redisObject是redis实现的对象模型，结构定义如下：

```
typedef struct redisObject {
    unsigned type:4;
    unsigned encoding:4;
    unsigned lru:LRU_BITS; /* LRU time (relative to global lru_clock) or
                            * LFU data (least significant 8 bits frequency
                            * and most significant 16 bits access time). */
    int refcount;
    void *ptr;
} robj;
```
