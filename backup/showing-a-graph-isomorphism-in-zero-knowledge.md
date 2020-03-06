---
title: Showing a Graph Isomorphism in Zero-Knowledge
tags: cryptography
---

结合图同构(Graph Isomorphism)问题，解释下零知识证明(Zero-Knowledge Proof-ZKP)，内容来自Nigel Smart所著Cryptography: An Introduction(3rd Edition)第25章。

Peggy(Prover)向Victor(Verifier)证明他知道图$G_1$和$G_2$的同构$\phi$，ZKP方案能够完成上述证明，并且不暴露$\phi$的任何信息。方案具体如下：

1. Commitment: Peggy生成$G_1,G_2$的另一同构$H$发给Victor;

2. Challenge-Response: Victor随机选择$b\in{1,2}$并要求Peggy给出$G_b,H$的同构;

3. Repeat: Peggy/Victor重复上述交互$n$次。

若Peggy每次都能给出正确的同构，当$n$足够大时Victor可以认为Peggy确实知道$\phi:G_1\rightarrow G_2$。假定Peggy并不知道$\phi$，则其每次生成的$H$只能是$G_1,G_2$之一的同构，由于Victor每次随机产生$b$，则Peggy通过验证的概率为$1/2$，$n$次都通过的概率为$1/{2^n}$，这即是其作弊的成功率。对于Victor而言，由于每轮的图$H$是Peggy随机生成的，且Victor只知道$G_b\rightarrow H$，无法凭此推出$\phi$。

形式化的证明上述协议符合Zero-Knowledge的要求是基于模拟(Simulation)的方法。所谓Simulation与图灵测试类似，我们假想一个与Peggy相同但不掌握$\phi:G_1\rightarrow G_2$的理想的Simulator，且其预先知道每轮Victor选择的$b$，也与Victor执行上述protocol。若Victor不能够分辨与Peggy的交互和与Simulator的交互，则认为该协议具有zero-knowledge的性质，若假设Victor无穷算力，称为perfect zero-knowledge proof，否则称为computational zero-knowledge proof。
