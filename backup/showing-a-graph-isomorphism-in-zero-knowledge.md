---
title: Showing a Graph Isomorphism in Zero-Knowledge
tags: cryptography
---

结合图同构(Graph Isomorphism)问题，解释下零知识证明(Zero-Knowledge Proof-ZKP)，内容来自Nigel Smart所著Cryptography: An Introduction(3rd Edition)第25章。

Peggy(Prover)向Victor(Verifier)证明他知道图$G_1$和$G_2$的同构$\phi$，ZKP方案能够完成上述证明，并且不暴露$\phi$的任何信息。方案具体如下：

1. Peggy生成$G_1,G_2$的另一同构$H$发给Victor;

2. Victor随机选择$b\in{1,2}$并要求Peggy给出$G_b,H$的同构;

3. Peggy/Victor重复上述交互$n$次。

若Peggy每次都能给出正确的同构，当$n$足够大时Victor可以认为Peggy确实知道$\phi:G_1\rightarrow G_2$。
