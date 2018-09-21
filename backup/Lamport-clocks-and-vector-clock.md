---
title: Lamport clocks and vector clocks
tags: distributed system
---

# happen-before relation

定义happen-before如下：
1. 如果事件A和事件B在同一个进程上，且A在B前执行，则A happen-before B；
2. 如果事件A和事件B在不同进程上，但A表示所在进程发送消息给B所在进程这一事件，且B表示接受消息这一事件，则A happen-before B。

从定义不难发现，happen-before关系有传递性，另一方面happen-before关系是偏序关系，如果既没有A happen-before B也没有B happen-before A，则称A, B为并发事件。

那么如何确定分布式系统中事件的happen-before关系呢，物理时钟不能承担此责，单机上时钟可能跳变，机器间的时钟更是没法统一无法比较。于是有以下两种逻辑时钟良好的描述事件发生时机并很方便确定事件的happen-before关系。

# Lamport clock

进程$P_i$维护自己的逻辑时钟$C_i$，假定初值都为0，该进程上事件A发生时，首先自增$C_i$，然后将当前逻辑时钟值作为该事件的Lamport clock($L(A)$)，这样同一个进程上的事件的Lamport clock大小关系就是该进程上的happen-before关系。

对于跨进程情况，假定事件A, B分别表示两个进程$P_i, P_j$上的消息发送和接受事件，$P_i$发送消息时需要带上发送事件的Lamport clock($L(A)$)，$P_j$收到后比较$L(A)$和自己的逻辑时钟$C_j$，如果$C_j < L(A)$，则更新$C_j=L(A)$；否则什么都不做。之后自增$C_j$并分配B的Lamport clock--$L(B)=C_j$。

举例如下图：

由以上Lamport clock的定义，不难看出，如果A happen-before B，则一定有$L(A) < L(B)$，但反之不成立。所以，基于Lamport clock，我们还是不能给出事件的happen-before关系。

# vector clock

向量时钟，假定系统中共有$n$个进程，则每个进程维护一个向量${C_i|1 \leq i \leq n}$，其中，$C_i$对应进程$P_i$。

对于进程$P_i$上的事件A，其向量时钟$VC(A)=[C_1, ..., ++C_i, ..., C_n]$。

对于进程间的交互事件A, B，事件A的向量时钟同上，发送时与Lamport clock一样带上VC(A)，B收到后比较VC(A)与其向量时钟的每个分量，并用两者中较大的值更新其对应分量，之后同样的自增自己的分量后分配给事件B作为其向量时钟。

举例如下：

由vector clock定义不难看出，如果VC(A)的各分量都小于等于VC(B)，则有A happen-before B，反之亦然。

# vector clock in Dynamo

Dynamo中使用vector clock解决last write win导致的写丢失问题
