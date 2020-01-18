---
title: perfect-secrecy
tags: cryptography
---

# cryptosystem
加密系统(cryptosystem)能够实现在不安全的信道上进行安全通信，如下图所示：

![](/files/cryptosystem.png)

一个加密系统是一个五元组$(\mathcal{P},\mathcal{K},\mathcal{C},\mathcal{E},\mathcal{D})$，其中

1. $(\mathcal{P},\mathcal{K},\mathcal{C})$分别表示明文(plaintext)，密钥(key)，密文(ciphertext)空间；

2. $\mathcal{E}=\{e_k:k\in \mathcal{K}\}$是加密算法$e_k:\mathcal{P}\rightarrow\mathcal{C}$集合；

3. $\mathcal{D}=\{d_k:k\in \mathcal{K}\}$是解密算法$d_k:\mathcal{C}\rightarrow\mathcal{P}$的集合；

且满足$\forall k\in \mathcal{K}, \forall p\in\mathcal{P}, d_k(e_k(p))=p$。

# perfect secrecy

如果一个加密系统中，密文不暴露任何明文的信息(长度除外)，则称该加密系统具有完美安全性(Perfect Secrecy)或者信息论安全(information-theoretic security)。

其中，密文不暴露明文信息指在密文的帮助下推算明文的概率分布与明文原始分布是相同的，具体分析如下。

用$p_P(x)$表示明文$x(x\in\mathcal{P})$出现概率；$p_K(k)$表示密钥为$k(k\in\mathcal{K})$的概率；且$p_P(x),p_K(k)$相互独立；$C(k)=\{e_k(x):x\in\mathcal{P}\}$表示由密钥$k$得到的密文集合。

则得到密文$y(y\in\mathcal{C})$的概率

\[ p_C(y)=\sum_{k:y\in C(k)}p_K(k)p_P(d_k(y)) \]

由明文$x$加密得到$y$的概率：

\[ p_C(y|x)=\sum_{k:x=d_k(y)}p_K(k) \]

由贝叶斯公式可计算出在已知密文为$y$解密得到明文$x$的概率为

\[ p_P(x|y)=\frac{p_P(x)\sum_{k:x=d_k(y)}p_K(k)}{\sum_{k:y\in C(k)}p_K(k)p_P(d_k(y))} \]

则perfect secrecy等价于$\forall x\in\mathcal{P},y\in\mathcal{C},p_P(x|y)=p_P(x)$，即$p_C(y)=p_C(y|x)$.

进一步，假定$\forall y\in\mathcal{C}, p_C(y)>0$，则$\forall x\in\mathcal{P},p_C(y|x)>0$，由于$y$是由$x$经$k$加密得到，则必然有$|\mathcal{K}|\geq|\mathcal{C}|$；另一方面每个密文都对应一个唯一的明文，故有$|\mathcal{C}|\geq|\mathcal{P}|$；综上，$|\mathcal{K}|\geq|\mathcal{P}|$，这意味着要实现perfect secrecy，密钥空间至少与明文空间一样大。
