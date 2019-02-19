---
title: race conditions of concurrent transactions
tags: database
---

本文列举并发事务可能发生的竞态条件(race condition)，主要参考自经典文献A critique of ANSI SQL isolation levels[@CritiqueANSISqlIsolation]，具体例子则来自DDIA[@ddia]。

# Dirty Write

Dirty Write指并发事务写入时覆盖了对方未提交的数据，如：

w1[x]...w2[x]...((c1 or a1) and (c2 or a2) in any order)

![](/files/dirty_write.PNG)

# Dirty Read

Dirty Read指并发事务读到了对方未提交的数据，如：

w1[x]...r2[x]...(a1 and c2 in any order)

实例如下：

![](/files/dirty_read.PNG)

# Lost Update

Lost Update是另一种常见的写写冲突，如：

r1[x]...w2[x]...w1[x]...c1

实例如下：

![](/files/lost_update.PNG)

如图，两个user并发的以read-modify-write方式increment counter，未发生dirty write，但是结果显然不正确。

与non-repeatable read怎么区分？？？

# Nonrepeatable Read

Nonrepeatable Read/Read Skew指并发事务两次读到的数据不一致(排除dirty read/dirty write)，因为两次读中间有事务提交，如：

r1[x]...w2[x]...c2...r1[x]...c1

实例如下：

![](/files/read_skew.PNG)

# Read Skew

History如：

r1[x]...w2[x]...w2[y]...c2...r1[y]...(c1 or a1)

# Write Skew 

History如：

r1[x]...r2[y]...w1[y]...w2[x]...(c1 and c2 occur)

# Phantom

r1[P]...w2[y in P]...c2...r1[P]...c1

实例如下：

![](/files/write_skew.PNG)

这种情况既不是dirty write(覆盖未提交数据)也不是lost update(覆盖已提交数据)。但又确实是一种异常，因为如果两个事务串行执行，后者不会成功，满足至少有一个医生在线的一致性约束。

与non-repeatable read不同，因为改变的是count值，而非
