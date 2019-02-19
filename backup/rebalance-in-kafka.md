---
title: rebalance in Kafka
tags: development
---

前面某篇文章总结了使用mysql作为task queue的两个优化，那么常用的消息队列组件在这个问题上是怎么设计的呢，本文解释下kafka的consumer与partition的分配策略。

首先，kafka中将同一个topic下的消息分成若干个partition存储，且保证每个消息仅被同一个consumer-group消费一次，实际上每个partition同时仅能被同一个group下的同一个consumer消费，但同一个consumer可以消费多个partition上的消息。

# fixed consumer group

如果consumer group是稳定的，那kafka采用两种策略进行分配，即range和round-robin，默认是range策略。Range策略按#partition/#consumer计算每个consumer消费的partition数，如10个分区，3个消费者，则分配策略是{0,1,2,3},{4,5,6},{7,8,9}。如果consumer比partition多，显然有一些consumer是没有任何partition对应的，理想情况就是consumer与partition一一对应，减少竞争。

# rebalance

Consumer可能加入或者离开(主动或者崩溃)group，partition也可能增减，这些都会引发rebalance操作。
