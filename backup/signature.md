---
title: signature
tags: cryptography
---

本篇总结下checksum, message digest, message authentication code, digital signature这几个概念的区别，各路网文时常一股脑的将这几个概念都叫做签名/验签。

抽象地讲，这几个概念的函数形式如下：

```
checksum = check(plaintext);
digest = md(plaintext);
mac = mac(plaintext, key);
signature = sign(plaintext, $SK$)
```

首先checksum和其余几个概念有本质不同，MD/MAC/Signature都是密码学原语，checksum则不是。Checksum和MD在接口形式上相近，都是直接从原文生成定长的tag(checksum/message digest)，二者的区别在于很容易构造特定checksum的原文，这对于MD而言是被设计目标所不允许的。Checksum只能用于检测传输过程中意外发生的bit翻转，而对于任何恶意的攻击无能为力；但是对MD算法，即使恶意的攻击者也则无法轻易构造特定message digest的原文。

MD/MAC/DS三者的区别是：MD是任何人可以生成，任何人可以校验；MAC是特定人可以生成，特定人校验，即仅持有密钥的双方可以生成/校验；DS则是持有私钥的人可以生成，任何人可以校验。
