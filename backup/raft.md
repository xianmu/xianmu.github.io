---
title: raft
tags: distributed system
---

流水账记录下raft的过程。

# start

1. 加载配置文件，从其中获取初始的集群配置信息；
2. 回放raft log构造state machine/last_index/last_term，如果有集群信息则覆盖配置文件里面的初始集群配置信息；
3. 设置自己为follower；
4. start AppendEntryService; start RequestVotingSerivce; start ElectionTimer;(分别对应5,6,7三步)
5. 接受AppendEntryRequest, 转follower，记录leader信息，反向查找到与leader匹配的最新日志条目，从leader同步后续条目。
6. 接受RequestVotingRequest, 回绝小于等于当前term或小于当前idx的投票请求, 否则投票并记录该term的投票决定。
7. election timeout, 转为candidate角色，++term, request all members for voting.
