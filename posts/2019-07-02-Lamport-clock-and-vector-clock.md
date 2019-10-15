---
title: Lamport clock and vector clock
tags: distributed system
---

# happened-before relation

定义happened-before[@lamport1978time]关系如下：

1. 如果事件A和事件B在同一进程上，且A在B前发生，则A happened-before B；

2. 如果事件A和事件B在不同进程上，但A表示进程发送消息，B表示对应的进程接受消息，则A happened-before B；

3. 传递性，即如果A happened-before B且B happened-before C，则A happened-before C。

happened-before是偏序关系，如果既没有A happened-before B也没有B happened-before A，则A, B为并发事件。

那么如何确定分布式系统中事件的happened-before关系呢？物理时钟不能承担此责，单机上时钟可能跳变，机器间更是没有统一的时钟无法比较。主要有以下两种逻辑时钟描述happened-before关系，即Lamport clock和vector clock。

# Lamport clock

进程$P_i$维护自己的逻辑时钟$C_i$，假定初值都为0，该进程上事件A发生时，首先自增$C_i$，然后将逻辑时钟作为该事件A的Lamport clock($L(A)$)，这样同一个进程上的事件的Lamport clock大小关系就是该进程上事件的happened-before关系。

对于进程间通信的情况，假定A, B分别表示进程$P_i, P_j$上的消息发送和接受事件。$P_i$发送消息时带上事件A的Lamport clock($L(A)$)；$P_j$在接受消息时比较$L(A)$和自己逻辑时钟$C_j$，如果$C_j < L(A)$，则更新$C_j=L(A)$，之后同样自增$C_j$并分配事件B的Lamport clock: $L(B)=C_j$;

如下图所示：

![](/files/lamport-clock.jpg)

由以上Lamport clock的生成方法，不难看出，如果A happened-before B，则有$L(A)\lt L(B)$，但反之不成立。所以，基于Lamport clock，我们还是不能确定事件的happened-before关系。

# vector clock

与Lamport clock使用一维的逻辑时钟不同，vector clock使用一个$n$维向量分别表示每个进程上的逻辑时钟。假定系统中一共有$n$个进程，每个进程维护一个向量${C_i|1\leq i\leq n}$，其中$C_i$对应进程$P_i$。

对于进程$P_i$上的事件A，其向量时钟$VC(A)=[C_1,..., ++C_i,..., C_n]$。

对于交互事件A, B，发送消息时同样带上$VC(A)$，$P_j$收到消息后比较$VC(A)$与其本地逻辑时钟的各个分量，并用两者中的较大值更新其本地逻辑时钟，之后同样自增$C_j$并分配本地逻辑时钟作为B的向量时钟。

如下图所示：

![vector clock [from wiki](https://en.wikipedia.org/wiki/Vector_clock)](/files/vector-clock.png)

由vector clock生成方法不难看出，如果$VC(A)\lt VC(B)$(每个分量都小于等于)，则有A happened-before B，反之亦然。

# conclusion

一言以蔽之，Lamport clock是happened-before关系的必要非充分条件，而vector clock是happened-before关系的充要条件。
