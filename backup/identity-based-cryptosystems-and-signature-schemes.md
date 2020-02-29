---
title: Identity-based cryptosystems and signature schemes
tags: cryptography
---

1984, Adi Shamir发表了一篇文章[@shamir1984identity]，提出了一种新的密码学工具，即Identity-Based Cryptosystem(IBC)，将PublicKey与UserIdentity合二为一，不再需要公钥/证书管理机构。

![identity-based cryptosystem](/files/identity-based-cryptosystem.png)

如图所示，第一步KeyGeneration根据用户Identity(图中i)生成其私钥(图中kd)；加密时直接用Identity加密，无需获取PublicKey。

类似的，Identity-based signature scheme的结构如下图所示：

![identity-based signature scheme](/files/identity-based-signature.png)

KeyGeneration同样根据用户Identity生成其私钥(kg)；用私钥对消息m签名；验证签名时直接使用sender's identity。

在文章中，Shamir给出了一个具体的Identity-based signature scheme，如下：

# KeyGen

公共参数$(n,e)$与RSA相同，UserIdentity为$i$对应的私钥为$g$，满足：

\[ g^e = i \text{mod} n\]

假设RSA problem(e-th root)是困难的，则除KeyGen center外其他人无法生成$g$。

# Sign

消息$m$的签名为$S(m)=(s,t)$:

\[ t = r^e \text{mod} n \]

\[ s = g\cdot r^{f(t,m)} \text{mod} n \]

其中$$r$为随机数，f$为一个单向函数。

# Verify

\[ s^e = i\cdot t^f(t,m)\text{ mod }n \]

正确性证明略。

# Application

Shamir对IBC/IBS的应用设想是这样的。
