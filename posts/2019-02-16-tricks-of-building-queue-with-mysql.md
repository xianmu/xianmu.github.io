---
title: tricks of building queue with mysql
tags: development
---

在做一个任务执行的小模块，大致功能是接受客户端提交任务，执行任务，返回执行结果给客户端。系统架构大体如下：

client<---->trackers<---->mysql<---->executors

系统包括tracker模块负责接收client请求(任务提交，结果查询)，mysql记录任务执行状态及相关参数，executor模块从mysql获取任务并具体执行。由于任务数可能会比较多，mysql这里进行了分库分表，假定分成$n$个表。不难看出，这里mysql也是充当tracker和executor间的任务队列。tracker和executor模块都是多实例的，提高可用性，同时方便集群扩容。tracker实现比较简单，根据用户请求的key，插入或者查找对应的表。

Executor没有使用固定的映射关系，而是由每个executor实例轮询所有的表，获取未执行的任务执行。sql语句如下：

```
    start transaction;
    select * from task_table_$i where status=CREATED limit 1 for update;
    -- put into executor pool; abort if failed;
    update task_table_$i set status=EXECUTING where id=***;
    commit;
```

为保证同一个任务不会被多个executor执行，这里使用了排它锁(select * for update)。但是，mysql的排它锁会阻塞后续事务([mysql8.0支持nowait和skip locked](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html))。

为此想了两种优化方案，第一种方案是每个executor使用不同的轮询顺序。实现上也比较简单，每个executor使用不同的与$n$互素的step size即可。如$n=10$，两个executor分别取step-size为3,7，则遍历顺序分别是{0,3,6,9,2,5,8,1,4,7}，{0,7,4,1,8,5,2,9,6,3}，相遇的概率较小。

第二种方案见于High Performance MySQL[@HighPerformanceMySQL3rd]，是一种通用的解决排它锁block的问题。核心思想是先update再select。sql语句如下：

```
    update task_table_$i set status=CLAIMED,executor=$my_id where executor=NULL and status=CREATED limit 1;
    select * from task_table_$i where executor=$my_id and status=CLAIMED;
```

这里甚至不需要事务。对于排它锁及优化方案，都需要检查并重新执行长时间无响应的任务，因为可能进程本身崩溃导致执行中的任务中断。
