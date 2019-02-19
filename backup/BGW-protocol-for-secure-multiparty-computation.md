---
title: BGW protocol for secure multiparty computation
tags: mutliparty computation
---

BGW是安全多方计算(SMC)中一个非常重要的协议，由Ben-Or, Goldwasser, Wigderson发表于1988年并以他们的名字命名。在前面一篇文章中曾经介绍过基于secret sharing的SMC方案的总体框架，这里不再重复。

# GenShare

BGW使用SSSS(Shamir's Secret Sharing Scheme)作为分配方案。

# Compute

有限域上的函数f总可以转换成等价的仅包含加法门和乘法门的arithmetic circuit。BGW给出了安全计算加法门和乘法门的方法，在arithmetic circuit上对所有的加法门乘法门应用即可得到整个circuit的计算结果且不泄露信息。

# add gate

加法门实现简单，因SSSS的特性，

# multiply gate

乘法门复杂些，因其不能仅根据本地分片完成计算。
