---
title: raft
tags: distributed system
---

Raft是一个较新的分布式一致性协议，与经典协议paxos相比，更好理解一些。更重要的，Ongero的博士论文Consensus: bridging theory and practice不仅介绍了raft协议，实际上该文十分详细地描述了分布式一致性协议在工程实践上的种种细节，而本文是该文的一个读后总结。

# Replicated State Machine

复制状态机(RSM-Replicated State Machine)是raft, paxos等多个协议的基础模型。简单讲，既然要解决分布式一致性(consensus)问题，那么如果各个副本具有相同的初始状态，应用相同的一连串变更操作，得到的当前状态也应当是一致的。如图：


在RSM模型下，各种一致性协议要解决的问题变成了如何保证log相同，并即时apply到状态机中。

# log replication

如果集群是固定的，那么raft会选择其中一个副本作为leader，并要求log的复制只能从leader流向follower，这样一来，follower的日志总是正确的，尽管有可能不是最新的，但是只要完成了全部日志的复制并apply到状态机中，那么所有副本都是一致的。

# leader election

Raft中，leader election是基于quorum的，即全部副本是平等的，没有leader的情况下，他们发起选举并搜集选票，当有一个副本搜集到多于半数的选票时宣告自己成为leader。




# start

1. 加载配置文件，从其中获取初始的集群配置信息；
2. 回放raft log构造state machine/last_index/last_term，如果有集群信息则覆盖配置文件里面的初始集群配置信息；
3. 设置自己为follower；
4. start AppendEntryService; start RequestVotingSerivce; start ElectionTimer;(分别对应5,6,7三步)
5. 接受AppendEntryRequest, 转follower，记录leader信息，反向查找到与leader匹配的最新日志条目，从leader同步后续条目。
6. 接受RequestVotingRequest, 回绝小于等于当前term或小于当前idx的投票请求, 否则投票并记录该term的投票决定。
7. election timeout, 转为candidate角色，++term, request all members for voting.
