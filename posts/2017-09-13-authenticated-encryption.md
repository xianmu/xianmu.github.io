---
title: authenticated encryption
tags: cryptography
---

认证加密(AE-authenticated encryption or AEAD-authenticated encryption with associated data)是对称密码系统的一种加密模式，它能够同时提供数据的保密性(confidentiality)，完整性(integrity)和真实性(authenticity)功能。

单一的看，数据保密性通常由加密算法提供；完整性可由两类hash算法提供，即摘要算法如md5, sha-1等，另一类则是消息认证码(MAC-message authentication code)；真实性通常也由MAC提供。然而，实践中的很多情况都需要同时保证这三种性质，比如医生给病人发送电子病历，病人不希望疾病信息泄露，也不能被篡改否则无法读取，还要确认该病历的确来自该医生。

既然，加密算法能够提供保密性，MAC能够提供完整性和真实性，那要实现同时提供三种性质的AEAD，是不是只要将二者结合起来使用就可以了。事实上没这么简单，尽管加密算法及MAC各自有着良好的安全性保证，但是结合之后的AEAD的安全性却不一定那么可靠。AEAD一共有以下四种经典的做法。

# EtM(encryption-then-mac)

![Encryption-then-MAC](/files/authenticated_encryption_etm.png)

EtM即先加密再MAC，明文作为加密算法的输入，MAC的输入是加密得到的密文，加密和MAC使用相同的密钥。整体输出由密文拼接MAC构成。

接受方则先验证MAC，通过验证再进行解密。

# E&M(encryption-and-mac)

![Encryption-and-MAC](/files/authenticated_encryption_eam.png)

E&M即同时做加密和MAC，加密算法及MAC的输入是明文和同一把密钥，整体输出也是由密文拼接MAC构成。

接收方先解密，然后对解密得到的明文做MAC校验，应当与收到的MAC相同。

# MtE(mac-then-encryption)

![MAC-then-Encryption](/files/authenticated_encryption_mte.png)

MtE顾名思义，即先做MAC再做加密，MAC的输入是明文及密钥key，而加密算法的输入是明文拼接MAC的整体，MtE的输出即加密算法的输出密文。

接收方先解密，然后对明文做MAC校验，应当与解密得到的MAC相同。

# AEAD
2008年之后，业内提出一些新的算法在内部同时实现加密和认证，摒弃了结合二者的路线，这些算法也被称为真正的AEAD算法。这么做的一个原因是单一算法实现比简单结合的方式效率更高。典型算法是AES-CCM和AES-GCM。
