---
title: group signature
tags: cryptography
---

Group Signature能够保护签名者的隐私。Group Signature满足以下几个性质：

1. Unforgeability: 只有group成员能够代表group签名；

2. Verifiability: Verifier能够验证该签名来自该group；

3. Anonymity: 但Verifier无法确定具体是哪个成员签的；

4. Traceability: 若发生争执，group manager可以确定签名的具体成员；

5. Unlinkability: 无法判断两个签名是否来自于同一个成员。

当然，group signature还有很多其他扩展，比如动态成员变更等等。

Chaum91[@chaum1991group]不仅给出了Group Signature的定义，还给出了四种方案，仅介绍下最简单的第一种方案。

# Simple Method

群管理员$\mathcal{Z}$为每个成员生成一组公私钥对，私钥发给各成员，所有公钥打乱后作为群签名的PublicKey。任意成员用其私钥中的一把签名消息，Verifier用公钥验证签名是否来自该group，但无法判断具体是哪个成员生成的签名。

这个方案有一个限制，即每把私钥只能使用一次，否则Verifier知道两次签名是来自同一个成员，不满足unlinkability。该方案下，公钥也比较长。

另一方面，除成员外，$\mathcal{Z}$也能签名，不过可以通过blinded public key方案解决这个问题。基于Elgamal的blinded public key方案如下，每个成员生成自己的key pair $<s_i, g^{s_i}>$并将$g^{s_i}$发送给$\mathcal{z}$，$\mathcal{z}$生成随机数$r_i$发回，群成员使用$s_ir_i$进行签名，Verifier使用$g^{s_ir_i}$验证。$\mathcal{Z}$无法生成签名；每个成员只需要存储自己的原始私钥$s_i$就够了；$r_i$泄露也不会导致$s_i$的暴露。
