---
title: redis-annotation-0
tags: database
---

阅读了一遍redis-4.0.9的源码，写一个总结。文件列表如下：

adlist.c/adlist.h                                                                                        
sds.c/sds.h
sdsalloc.h  -------未读

dict.c/dict.h

intset.c/intset.h

ziplist.c/ziplist.h

------------------------------------------------------------

t_hash.c

t_list.c

t_set.c

t_stream.c

t_string.c

t_zset.c

ae.c/ae.h
ae_epoll.c
ae_evport.c
ae_kqueue.c
ae_select.c
anet.c/anet.h

zmalloc.c/zmalloc.h

zipmap.c/zipmap.h

hyperloglog.c

aof.c    

atomicvar.h

bio.c/bio.h

bitops.c

blocked.c

childinfo.c

cluster.c/cluster.h

config.c/config.h

crc16.c
crc64.c/crc64.h

db.c

debug.c
debugmacro.h

defrag.c

endianconv.c/endianconv.h

evict.c

expire.c

fmacros.h

geo.c/geo.h
geohash.c/geohash.h
geohash_helper.c/geohash_helper.h

help.h



latency.c/latency.h

lazyfree.c

listpack.c/listpack.h
listpack_malloc.h

localtime.c

lolwut.c
lolwut5.c

lzf.h
lzf_c.c
lzf_d.c
lzfP.h

memtest.c

module.c

multi.c

networking.c

notify.c

object.c

pqsort.c/pqsort.h

pubsub.c

quicklist.c/quicklist.h

rand.c/rand.h

rax.c/rax.h
rax_malloc.h

rdb.c/rdb.h

redis-benchmark.c

redis-check-aof.c

redis-check-rdb.c

redis-cli.c

redisassert.h

redismodule.h

release.c

replication.c

rio.c/rio.h

scripting.c

sentinel.c

server.c/server.h

setproctitle.c

sha1.c/sha1.h

siphash.c

slowlog.c/slowlog.h

solarisfixes.h

sort.c

sparkline.c/sparkline.h

stream.h

syncio.c


testhelp.h

util.c/util.h



# zslist
