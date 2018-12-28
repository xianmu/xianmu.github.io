---
title: secret sharing and secure multiparty computation
tags: security
---

在之前的文章中介绍过安全多方计算问题(SMC-Secure Multiparty Computation)，也介绍过Shamir's Secret Sharing Scheme(SSSS)。本篇介绍用secret sharing解决SMC问题的框架，具体的实现方案在后续文章中再做介绍。

如前所述，SMC要求一组参与方合作计算一个函数而不暴露各自的输入，而secret sharing能够保证在无共谋的情况下的数据安全，于是便有了以下框架。

为简便，仅讨论两方的情况，假定Alice和Bob想要计算$z=f(x,y)$($x,y$分别为他们的输入)。计算过程分为三步，GenShare，Compute，Reconstruct，具体如下：

1. $GenShare$: Alice, Bob各自执行secret sharing生成$x,y$的share $x_i,y_i(1\leq i\leq n)$，并分发给各计算节点$C_j,1\leq j\leq n$。

2. $Compute$: 为计算$z=f(x,y)$，各计算节点在分片上执行计算(可能需要交互)，执行结果是$z$的分片形式，以保证结果对计算节点保密。

3. $Reconstruct$: Alice, Bob从计算节点获取足够的secret share，恢复结果$z$。

由于输入$x,y$，输出$z$都是以secret share的形式存在，所以无共谋的情况下，各计算节点不能恢复它们；中间计算过程也都是基于分片，保证泄露的信息不足以暴露输入输出。

如何将原函数$f$转换成secret share上的等价计算过程并保证计算结果仍然是secret share形式是各个具体协议考虑的内容，在后续的文章再具体讨论。

以加法$f(x,y)=x+y$为例，采用additive secret sharing方案，并假设计算节点数$n=3$。

1. Alice计算$x$的分片，$x_1,x_2,x_3=x-x_1-x_2$($x_1,x_2$为随机值)，并分发给计算节点；Bob同理；计算节点$C_i$持有$x_i,y_i$。

2. 加法的计算过程较为简单，直接本地执行$x_i+y_i$，即$z_i$。

3. Reconstruct直接获取所有share，求和即$z=x+y$。

当然两方的加法不是一个太好的例子，因为结果会暴露输入，Alice或者Bob用和减去$x$或者$y$就可以得到对方的输入了。任何SMC方案都无法保护那些从输出能够推出的信息。

加法还是比较简单的，只需要本地计算，其他算子如乘法等等可能就需要计算节点间交互才能完成计算。
