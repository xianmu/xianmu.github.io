---
title: redis-annotation-1
tags: database
---

阅读了一遍redis-4.0.9的源码，写一个总结。

# adlist

adlist是redis中提供的双链表(非循环)实现，堪称教科书般的实现，阅读难度不大。首先是基本的数据结构定义，包括listNode, list, listIter。

```
typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;

typedef struct listIter {
    listNode *next; // maybe confusing
    int direction;
} listIter;

typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr); // duplicate value in listNode, default dup void*
    void (*free)(void *ptr); // free value in listNode, default no op
    int (*match)(void *ptr, void *key); // value=?key, default test equality of void*
    unsigned long len;
} list;
```

list结构体中通过函数指针模拟了成员函数的功能。adlist的实现中不带有dummy object，空链表的head=tail=NULL。接口实现都比较简单，省略代码。

# sds

sds(simple dynamic string)是redis中动态字符串的实现，可以存储二进制数据，且自动管理内存。

类型sds实际就是char*的typedef，实现上会在前面加header记录可用内存长度及字符串长度，定义如下。

```
typedef char *sds;

struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

为节省内存用量，sds针对不同长度的字符串定义了5种不同的头部，sdshdr5/sdshdr8/sdshdr16/sdshdr32/sdshdr64。这些结构体中，len表示字符串长度，alloc表示最大容纳的长度(如注释，sds实际占用的空间比alloc多header以及NULL的1B)。flags的低3bit表示header类型，高5bit unused。sdshdr5略有不同，他使用flags的高5bit存储字符串长度(len)，而不预留空间，所以也没有alloc字段；因此，empty string默认是type 8，而非type 5，以便于append。最后的buf指针即实际字符串指针。__attribute__((__packed__))是gcc特有的扩展，表示对结构体使用紧凑布局，而不进行字节对齐的优化。

接口代码都比较简单，难度不大，略去。

# dict

dict是redis中字典(hashtable(non-multi))的实现。主要的数据结构如下：

```
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next;
} dictEntry;

typedef struct dictht {
    dictEntry **table;
    unsigned long size; 
    unsigned long sizemask;
    unsigned long used; 
} dictht;
```

Redis的dict实现，使用拉链法(单链表)解决冲突，所以dictEntry除包含key-value外，还包括next指针指向同一个hash桶中的下一个entry, dictht中的table类型相应的是dictEntry**。size为table数组的length，总是2的幂，sizemask自然就是size-1，用于与hash(key)结果进行与操作确定bin index。used是当前包含的kv数量，即dictEntry数。

```
typedef struct dictType {
    uint64_t (*hashFunction)(const void *key);
    void *(*keyDup)(void *privdata, const void *key);
    void *(*valDup)(void *privdata, const void *obj);
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);
    void (*keyDestructor)(void *privdata, void *key);
    void (*valDestructor)(void *privdata, void *obj);
} dictType;

typedef struct dict {
    dictType *type;
    void *privdata; // private data called by functions in dictType
    dictht ht[2];
    long rehashidx; // rehashing not in progress if rehashidx == -1 
    unsigned long iterators; // number of iterators currently running(safe iterators)
} dict;
```

dictType是一些函数指针的集合，含义都比较清晰。dict包含了两个dictht，这个是为了rehash。其他字段如注释，

```
typedef struct dictIterator {
    dict *d;
    long index; // index of current hash bin
    int table; // d->ht[0] or d->ht[1]
    int safe; // safe iterator prevents rehashing 
    dictEntry *entry, *nextEntry;
    long long fingerprint; // represent dict state at a given time, prevent unsafe iterators from performing forbidden operations
} dictIterator;
```

最后是迭代器，几个字段的含义如注释。fingerprint表示dict状态，由dict中的几个字段(ht[0|1].table|size|used)hash得到。

接口的代码都比较清晰。默认的hash函数是siphash。

有一点疑问，如下接口dictRehashMilliseconds在一段时间内持续进行rehash，这段代码里面rehashes有什么作用，为何每次加100？这个rehashes作为返回值，在代码中也没有发现在哪里用到。

```
int dictRehashMilliseconds(dict *d, int ms) {
    long long start = timeInMilliseconds();
    int rehashes = 0; // useless???

    while(dictRehash(d,100)) {
        rehashes += 100;
        if (timeInMilliseconds()-start > ms) break;
    }
    return rehashes;
}
```

dictScan算法没看太明白？

resize部分的逻辑也没看彻底？

# zskiplist

redis的skiplist实现在server.h(数据结构)及t_zset.c(函数)中，基础数据结构如下:

```
typedef struct zskiplistNode {
    sds ele; // data
    double score; // for sorting, can be duplicated
    struct zskiplistNode *backward; // only the lowest level
    struct zskiplistLevel {
        struct zskiplistNode *forward;
        unsigned int span; // #nodes crossed reaching to forward
    } level[];
} zskiplistNode;

typedef struct zskiplist {
    struct zskiplistNode *header, *tail;
    unsigned long length; // #nodes
    int level; // max level
} zskiplist;
```

zskiplist的实现带有头结点。大部分都比较直接，span表示到达下一个节点中间跳过的节点数，实现中有一个rank的概念，rank实际是节点的index，不难发现rank即为遍历过程中span的和。

## robj

zskiplist部分接口涉及到一个叫robj的结构，先看下这个结构：

```
typedef struct redisObject {
    unsigned type:4; // string|list|set|zset|hash
    unsigned encoding:4;
    unsigned lru:LRU_BITS; /* LRU time (relative to global lru_clock) or
                            * LFU data (least significant 8 bits frequency
                            * and most significant 16 bits access time). */
    int refcount;
    void *ptr; // underlying struct
} robj;
```

robj为不同的数据结构提供了统一的接口，robj内部记录具体是什么类型的数据结构，使用什么实现方式(encoding)。(lru这里先不细看)

# intset

intset是redis中实现的整数集合(non-multi)，数据结构如下：

```
typedef struct intset {
    uint32_t encoding; // sizeof int, default int16
    uint32_t length; // #elements
    int8_t contents[];
} intset;
```

其中，encoding表示每个int用几个byte表示，contents实际保存集合中的整数，length为集合大小。intset中的整数按从小到大有序存储。

# ziplist

ziplist是redis中实现的压缩列表，ziplist实际是一个编码的双向链表，可以存储整数或者字符串，存储效率高。源码里面没有显式定义ziplist的结构，大致如下：

```
struct ziplist {
  uint32_t size; // total memory size including size 
  uint32_t tail_offset; // for O(1) push
  uint16_t length; // #elements
  entry entry_list[]; 
  uint8_t end;
};
```

上面entry同样没有显式定义，大致结构是<prevrawlen><len><data>，代码中有一个zlentry结构，用于还原entry结构，如下：

```
typedef struct zlentry {
    unsigned int prevrawlensize; // size of prevrawlen
    unsigned int prevrawlen;     // previous entry len. 
    unsigned int lensize;        // size of len.
    unsigned int len;            // length of this entry.
    unsigned int headersize;     // prevrawlensize + lensize. 
    unsigned char encoding; 
    unsigned char *p;            // pointer to the start of the entry
} zlentry;
```

理解了数据结构，其他接口实现的代码也都比较清晰。
