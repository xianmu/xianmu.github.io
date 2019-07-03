---
title: two phase commit
tags: distributed system
---

Two Phase Commit，两阶段提交(2PC)是一个经典的保证分布式事务一致性的算法。

2PC是一个有中心系统，事务操作均提交到coordinator上，并由其协调各RM执行操作。顾名思义，2PC将事务执行过程分位两个阶段，prepare phase和commit phase。

Prepare phase, coordinator向各participant发送事务操作，各participant分配所需资源，成功分配返回yes，否则返回no。Commit phase, coordinator搜集前一阶段各participant的反馈，如果有no则abort事务，否则通知所有participant该事务commit。

# exceptions

1. prepare phase, coordinator发了消息后crash，participant收到了消息并响应。响应的participant无法知道第一阶段的结果，无法推进事务执行，既不能commit也不能abort。可以重新选举coordinator，搜集各participant的结果，继续执行。

2. prepare phase, 某个participant crash，无论其是否收到消息，coordinator都无法知道其执行情况，系统无法进入第二阶段。Coordinator可以通过timeout然后abort事务。

3. commit phase, coordinator发送了部分消息后crash，由于未收到消息的participant不知道事务最终始commit还是abort，所以系统block。
