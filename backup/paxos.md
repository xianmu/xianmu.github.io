---
title: paxos
tags: distributed system
---

Paxos[@lamport1998part][@lamport2001paxos]大名鼎鼎，几乎是共识(consensus)协议的代名词。本文是我对paxos(basic-paxos)的理解。

# consensus

Consensus意指分布式环境中一组进程就某个问题达成相同的认识，如谁是leader，x=?，事务commit or rollback。

# Replicated State Machine
