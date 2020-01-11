---
title: perfect-secrecy
tags: cryptography
---

# cryptosystem
加密系统(cryptosystem)能够实现在不安全的信道上进行安全通信，如下图所示：

![](/files/cryptosystem.png)

一个加密系统是一个五元组$(\mathcal{P},\mathcal{K},\mathcal{C},\mathcal{E},\mathcal{D})$，其中$(\mathcal{P},\mathcal{K},\mathcal{C})$分别表示明文(plaintext)，密钥(key)，密文(ciphertext)空间；$(\mathcal{E},\mathcal{D})$分别表示加密解密算法；且满足$\mathcal{D}_k(\mathcal{E}_k(p))=p$。
