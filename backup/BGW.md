---
title: Completeness theorems for non-cryptographic fault-tolerant distributed computation
tags: mutliparty computation
---

BGW[@ben1988completeness]是安全多方计算(SMC)中一个经典协议，由Ben-Or, Goldwasser, Wigderson发表于1988年并以他们的名字命名。在前面一篇文章中曾经介绍过基于secret sharing的SMC方案的[总体框架](2018-12-28-secret-sharing-and-secure-multiparty-computation.html)，这里不再重复。

# GenShare

BGW使用[SSSS](2018-12-03-shamir-secret-sharing-scheme.html)作为分片方案。如图，Alice和Bob分别将$a,b$分成$n=3$个share并分发给$P_1,P_2,P_3$, 并令$k=2$，即拥有其中两个分片可恢复$a(b)$：

# Compute

有限域上的函数$f$总可以转换成等价的仅包含加法门和乘法门的arithmetic circuit。BGW给出了安全计算加法门和乘法门的方法，在arithmetic circuit上对所有的加法门乘法门应用即可得到整个circuit的计算结果且不泄露信息。

## add gate

加法门实现简单，因SSSS的特性，加法仅需本地计算就可以完成。假设Alice和Bob的分片方案分别是，$f(x)=a+\sum_1^{k-1}a_ix^i$和$g(x)=b+\sum_1^{k-1}b_ix^i$，并且在相同的$x$处生成share，如$(x_1,f(x_1)),(x_2,f(x_2)),...,(x_n,f(x_n))$和$(x_1,g(x_1)),(x_2,g(x_2)),...,(x_n,g(x_n))$。不难发现，$(x_1,(f+g)(x_1)),(x_2,(f+g)(x_2)),...,(x_n,(f+g)(x_n))$均落在(f+g)(x)上，且其常数项即$a+b$，所以相加后的分片可以用于恢复$a+b$。

## multiply gate

乘法门复杂些，因其不能仅根据本地分片完成计算。延用上面的例子，首先$(f\cdot g)(x)$的常数项确为$a\cdot b$，但是要注意其次数为$2(k-1)$，需要$2k-1$个点才能恢复结果,当$n\geq 2k-1$时还没有问题，由于整个电路可能很大，每次乘法都会导致次数翻倍，最终超过$n$，没有足够的share完成插值计算。问题变成了如何得到一个常数项为$ab$而次数不变$(k-1)$的新随机曲线$h(x)=ab+\sum_1^{k-1}h_ix^i$。

BGW仍然是先计算本地分片的乘积，再对结果进行降次。

### degree reduction 

假设，$h(x)=f\cdot g(x)=ab+\sum_1^{2(k-1)}h_ix^i$，那么$(x_1,(f\cdot g)(x_1)),(x_2,(f\cdot g)(x_2)),...,(x_n,(f\cdot g)(x_n))$为$h(x)$上的$n$个点。另设，$k(x)=ab+\sum_1^{k-1}h_ix^i$，即$h(x)$的前$k$项，则存在$n\mul n$的矩阵$A$使得，$[k(x_1),...,k(x_n)]=[h(x_1),...,h(x_n)]A$。

证明：

假设，$H=[h_0,...,h_{k-1},...,h_{2(k-1)},0,...,0]$, $K=[h_0,...,h_{k-1}]$, V为$n\mul n$阶Vandermonde矩阵，$P$为投影函数$P(x_1,...,x_n)=(x_1,...,x_k,0,...,0)$。则有，$H\cdot V=S, H\cdot P=K, K\cdot V=R$，可推出$S\cdot (V^{-1}PV)=R$，即$A=V^{-1}PV$。

### randomization

上面这个方案还有一点问题，结果曲线$k(x)$不是随机的，由于$h(x)$是可约的，因其是$f(x),g(x)$的乘积，本身就不是随机的，截取其前$k$项也不是随机的。SSSS本身支持纵向更新分片，直接应用一下即可。
