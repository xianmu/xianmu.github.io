---
title: consistency models
tags: distributed system 
---

分布式系统中，一致性模型描述了并发读写时系统的预期响应，换言之，如果分布式系统满足某种一致性模型，那么读写请求的响应应当符合该一致性模型定义的规则。以下结合具体例子介绍几种主要的一致性模型。

# linearizability
Linearizability，即线性一致性，此为实践中最强的一致性模型，要求某时刻客户端从系统中读取到$x$的值后，之后从系统中读取的$x$值都更新，而不会发生回退。从效果上，满足线性一致性的分布式系统，好像是一个个的原子的执行了各个读写操作。

# sequential consistency

# causal consistency

# PRAM consistency

# eventual consistency
