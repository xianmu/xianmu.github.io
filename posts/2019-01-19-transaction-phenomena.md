---
title: transaction phenomena
tags: database
---

本文列举事务隔离性的各种异像(phenomena)。A critique of ANSI SQL isolation levels[@CritiqueANSISqlIsolation]是事务隔离性方面不可不读的经典文献，其总结了各种phenomena和isolation level的形式化描述及对应关系；本文的例子来自于文献[@javaPersistence]。

# Dirty Write

Dirty Write指并发事务写入时覆盖了对方未提交的数据，history[@CritiqueANSISqlIsolation]形如：

P0: w1[x]...w2[x]...((c1 or a1) and (c2 or a2) in any order)

例如，假定x,y上有约束x=y，事务T1,T2分别修改x,y，并发history为:

H0: w1[x]w2[x]w2[y]c2 w1[y]c1

显然该history违背了上述约束，如果串行执行，始终有x=y。

再举一个具体的例子[@javaPersistence]如下图：

![](/files/dirty_write.PNG)

# Dirty Read

Dirty Read指并发事务读到了对方未提交的数据，history如下：

A1: w1[x]...r2[x]...(a1 and c2 in any order)

事务T1写x，事务T2读x之后T1回滚，导致T2的结果失效。

A1是dirty read的strict interpretation，即history形如A1必定有dirty read异常，但反之不成立，实际上A1可能放过一些异常，如下例，

H1: r1[x=50]w1[x=10]r2[x=10]r2[y=50]c2 r1[y=50]w1[y=90]c1

从x账户转40到y账户，初始值x=y=50，T2观察到了不一致状态(x=10,y=50), 而H1却不违背A1。实际上通常采用一种更宽松(broad interpretation)的history，即：

P1: w1[x]...r2[x]...((c1 or a1) and (c2 or a2) in any order)

注意，H1是违背了P1的。后续讨论也都基于各种phenomena的broad interpretation。

关于dirty read，再举一个具体的例子如下：

![](/files/dirty_read.PNG)

# Lost Update

如字面意思，Lost Update指并发事务执行完只有一个更新生效，另一个更新丢失，history如下：

P4: r1[x]...w2[x]...w1[x]...c1

例如，有history:

H4: r1[x=100]r2[x=100]w2[x=120]c2 w1[x=130]c1

最终结果只包含了T1的修改，而T2的更新丢失。

实例如下：

![](/files/lost_update.PNG)

如图，两个user并发的以read-modify-write方式increment counter，未发生dirty write，但是结果显然不正确。

# Nonrepeatable Read

Nonrepeatable Read/Fuzzy Read指并发事务两次读到的数据不一致，因为两次读中间有事务提交，history形如：

A2: r1[x]...w2[x]...c2...r1[x]...c1

实例如下：

![](/files/nonrepeatable_read.PNG)

# Read Skew

以上phenomena都只涉及单个数据项的读写，后续三个phenomena则涉及多个数据项的读写一场。

Read skew指，history如：

A5A: r1[x]...w2[x]...w2[y]...c2...r1[y]...(c1 or a1)

实例如下：

![](/files/read_skew.PNG)

# Write Skew 

Write skew指，history如：

A5B: r1[x]...r2[y]...w1[y]...w2[x]...(c1 and c2 occur)

实例如下：

![](/files/write_skew.PNG)

# Phantom

Read skew/write skew涉及两个数据项的读写，phantom则是指事务观察到了由谓词限定的集合发生变化，这种变化的原因不局限于insert，也包括update/delete，对应的history形如：

A3: r1[P]...w2[y in P]...c2...r1[P]...c1

实例如下：

![](/files/phantom_read.PNG)

这种情况既不是dirty write(覆盖未提交数据)也不是lost update(覆盖已提交数据)。但又确实是一种异常，因为如果两个事务串行执行，后者不会成功，满足至少有一个医生在线的一致性约束。

