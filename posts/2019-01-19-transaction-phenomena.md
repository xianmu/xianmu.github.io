---
title: transaction phenomena
tags: database
---

本文列举事务隔离性的各种异像(phenomena)。A critique of ANSI SQL isolation levels[@CritiqueANSISqlIsolation]是事务隔离性方面不可不读的经典文献，其总结了各种phenomena和isolation level的形式化描述及对应关系；此外，本文的一些具体例子来自于文献[@javaPersistence]。

# Dirty write

Dirty write指并发事务写入时覆盖了对方未提交的数据，history[@CritiqueANSISqlIsolation]形如：

P0: w1[x]...w2[x]...((c1 or a1) and (c2 or a2) in any order)

例如，假定x,y上有约束x=y，事务T1,T2分别修改x,y，并发history为:

H0: w1[x]w2[x]w2[y]c2 w1[y]c1

显然该history违背了上述约束，如果串行执行，始终有x=y。

再举一个具体的例子[@javaPersistence]如下图：

![](/files/dirty_write.PNG)

# Dirty read

Dirty read指并发事务读到了对方未提交的数据，history如下：

A1: w1[x]...r2[x]...(a1 and c2 in any order)

事务T1写x，事务T2读x之后T1回滚，导致T2的结果失效。

A1是dirty read的strict interpretation，即history形如A1必定有dirty read异常，但反之不成立，实际上A1可能放过一些异常，如下例，

H1: r1[x=50]w1[x=10]r2[x=10]r2[y=50]c2 r1[y=50]w1[y=90]c1

从x账户转40到y账户，初始值x=y=50，T2观察到了不一致状态(x=10,y=50), 而H1却不违背A1。实际上通常采用一种更宽松(broad interpretation)的history，即：

P1: w1[x]...r2[x]...((c1 or a1) and (c2 or a2) in any order)

不难看出，上述H1是违背了P1的。

关于dirty read，再举一个具体的例子[@javaPersistence]如下：

![](/files/dirty_read.PNG)

# Lost Update

如字面意思，Lost Update指并发事务执行完只有一个更新生效，另一个更新丢失，history如下：

P4: r1[x]...w2[x]...w1[x]...c1

例如，有history:

H4: r1[x=100]r2[x=100]w2[x=120]c2 w1[x=130]c1

最终结果只包含了T1的修改，而T2的更新丢失。实际上，P4会被后面的nonrepeatable read(P2)包含，但是其可用于区别read committed和repeatable read之间的隔离级别。

实例如下：

![](/files/lost_update.PNG)

# Nonrepeatable read

Nonrepeatable read/Fuzzy read指并发事务两次读到的数据不一致，因为两次读中间有事务提交，history形如：

A2: r1[x]...w2[x]...c2...r1[x]...c1

P2: r1[x]...w2[x]...((c1 or a1) and (c2 or a2) in any order)

A2, P2分别为non-repeatable read的strict/broad interpretation。同样的，可以从下面这个history中看出，为什么应该采用broad interpretation即P2取代A2：

H2: r1[x=50]r2[x=50]w2[x=10]r2[y=50]w2[y=90]c2 r1[y=90]c1

T1观察到$x+y=140$的不一致状态，由于T1没有再次r1[x]，所以其并不违背A2，而P2可以排除此类异常。

实例如下：

![](/files/nonrepeatable_read.PNG)

# Read Skew

与前面的phenomena不同，read skew涉及多个数据项的一致性约束。通常是因为多个数据读取过程中发生了并发事务对这些数据项的更新，history如：

A5A: r1[x]...w2[x]...w2[y]...c2...r1[y]...(c1 or a1)

实例如下：

![](/files/read_skew.PNG)

# Write Skew 

Write skew与read skew类似，也是在多个数据项上的一致性约束由于并发事务分别修改了部分数据项受到破坏，history如：

A5B: r1[x]...r2[y]...w1[y]...w2[x]...(c1 and c2 occur)

实例如下：

![](/files/write_skew.PNG)

# Phantom

Phantom则是指事务观察到了由谓词限定的集合发生变化，这种变化的原因不局限于insert，也包括update/delete，对应的history形如：

A3: r1[P]...w2[y in P]...c2...r1[P]...c1

P3: r1[P]...w2[y in P]...((c1 or a1) and (c2 or a2) in any order)

A3, P3分别为phantom的strict/broad interpretation，二者的区别可以由如下H3反映：

H3: r1[P]w2[insert y in P]r2[z]w2[z]c2 r1[z]c1

其中$z$为谓词P限定的集合的size，由于中间T2插入了$y$，T1开始读取的集合和最后读取的$z$值不一致。

实例如下：

![](/files/phantom_read.png)

