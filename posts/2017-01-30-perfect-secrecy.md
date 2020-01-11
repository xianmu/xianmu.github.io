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

