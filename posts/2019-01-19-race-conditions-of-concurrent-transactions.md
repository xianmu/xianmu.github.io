---
title: race conditions of concurrent transactions
tags: database
---

本文列举并发事务可能发生的竞态条件(race condition)，主要参考自经典文献A critique of ANSI SQL isolation levels[@CritiqueANSISqlIsolation]，具体例子则来自DDIA[@ddia]。

# Dirty Write

Dirty Write指并发事务写入时覆盖了对方未提交的数据，history形如：

P0: w1[x]...w2[x]...((c1 or a1) and (c2 or a2) in any order)

例如，假定x,y上有约束x=y，事务T1,T2分别修改x,y，并发history为w1[x]w2[x]w2[y]c2 w1[y]c1，显然该history违背了一致性，如果串行执行，始终有x=y。

具体的例子如下图：

![](/files/dirty_write.PNG)

# Dirty Read

Dirty Read指并发事务读到了对方未提交的数据，如下面的history：

A1: w1[x]...r2[x]...(a1 and c2 in any order)

事务T1写x，事务T2读x之后T1回滚，导致T2的结果失效。

## broad interpretation 

A1仍然可能放过一些异常，实际上通常采用一种更放松(broad interpretation)一点的history，即：

P1: w1[x]...r2[x]...((c1 or a1) and (c2 or a2) in any order)

如下例，从x账户转40到y账户，初始值x=y=50。

H1: r1[x=50]w1[x=10]r2[x=10]r2[y=50]c2 r1[y=50]w1[y=90]c1

注意，T2观察到了不一致状态(x=10,y=50)。H1违背了P1，但是却不违背A1。

Dirty Read更具体的例子如下：

![](/files/dirty_read.PNG)

# Cursor Lost Update

# Lost Update

Lost Update是另一种常见的写写冲突，history如下：

P4: r1[x]...w2[x]...w1[x]...c1

例如，有如下history:

H4: r1[x=100]r2[x=100]w2[x=120]c2 w1[x=130]c1

最终结果只包含了T1的修改，而T2的更新丢失。

实例如下：

![](/files/lost_update.PNG)

如图，两个user并发的以read-modify-write方式increment counter，未发生dirty write，但是结果显然不正确。

与non-repeatable read怎么区分？？？

# Nonrepeatable Read

Nonrepeatable Read/Fuzzy Read指并发事务两次读到的数据不一致(排除dirty read/dirty write)，因为两次读中间有事务提交，如：

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
