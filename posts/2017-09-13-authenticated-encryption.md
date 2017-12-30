---
title: authenticated encryption
tags: cryptography
---

认证加密(AE-authenticated encryption or AEAD-authenticated encryption with associated data)是对称密码系统的一种加密模式，它能够同时提供数据的保密性(confidentiality)，完整性(integrity)和真实性(authenticity)功能。

单一的看，数据保密性通常由加密算法提供；完整性可由两类hash算法提供，即摘要算法如md5, sha-1等，另一类则是消息认证码(MAC-message authentication code)；真实性通常也由MAC提供。然而，实践中的很多情况都需要同时保证这三种性质，比如医生给病人发送电子病历，病人不希望疾病信息泄露，也不能被篡改否则无法读取，还要确认该病历的确来自该医生。

既然，加密算法能够提供保密性，MAC能够提供完整性和真实性，那要实现同时提供三种性质的AEAD，是不是只要将二者结合起来使用就可以了。事实上没这么简单，尽管加密算法及MAC各自有着良好的安全性保证，但是结合之后的AEAD的安全性却不一定那么可靠。AEAD一共有以下四种经典的做法。

# E&M(encryption-and-mac)

![Encryption-and-MAC](/files/Authenticated_Encryption_EaM.png)

E&M即同时做加密和MAC，加密算法及MAC的输入是明文和同一把密钥，整体输出也是由密文拼接MAC构成。

接收方先解密，然后对解密得到的明文做MAC校验，应当与收到的MAC相同。

SSH2中使用了该模式。

E&M不保证密文的完整性，这使得某些CCA攻击成为可能。明文完整性可以得到校验。MAC可能反映明文信息。

E&M能够并行做加密和MAC，理论上效率高于EtM和MtE。

# MtE(mac-then-encryption)

![MAC-then-Encryption](/files/Authenticated_Encryption_MtE.png)

MtE顾名思义，即先做MAC再做加密，MAC的输入是明文及密钥key，而加密算法的输入是明文拼接MAC的整体，MtE的输出即加密算法的输出密文。

接收方先解密，然后对明文做MAC校验，应当与解密得到的MAC相同。

SSL/TLS中使用了该模式。

MtE不提供密文完整性，不解密的情况下无法知道密文是否被伪造或者篡改。MAC不反映任何明文信息，因为MAC已经被加密了。

理论证明，如果MtE使用足够强度的MAC，如HMAC或CBC-MAC，则安全性同EtM是一样的。Krawczyk2001[@krawczyk2001order]给出了一个例子，即使加密算法满足Shannon perfect secrecy，与MAC采用MtE方式组合的情况下，得到的协议仍然是不安全的，E&M同理。

MtE的实现还有一些陷阱值得注意，简单的实现，即解密后校验MAC失败立刻返回，不能抵抗padding-oracle攻击，许多MtE的实现都有此问题。MAC校验错误和padding错误应当同时返回。

# EtM(encryption-then-mac)

![Encryption-then-MAC](/files/Authenticated_Encryption_EtM.png)

EtM即先加密再MAC，明文作为加密算法的输入，MAC的输入是加密得到的密文，加密和MAC使用相同的密钥。整体输出由密文拼接MAC构成。

接受方则先验证MAC，通过验证再进行解密。

IPSec中使用了该模式。

EtM保证密文完整性和真实性，不会尝试解密无效密文，天然地避免了CCA攻击，同时也可以抵御Dos攻击。另一方面，由于MAC是对密文操作得到的，不反映明文任何信息，这也避免了MAC不提供保密性导致的问题。理论分析表明，EtM的安全性高于E&M和MtE。

注意，这里还有一个细节问题，Ferguson和Schneier在Practical Cryptography[@practical-cryptography]一书中指出，MAC的输入需要包含加密时使用的initial-vector，否则攻击者可能构造MAC检测不出来的篡改。针对一个有问题的IPsec实现，他们给出了一个攻击。

以上三者，E&M, MtE和EtM的安全性分析详见Bellare&Namprempre2000[@bellare-namprempre2000]。

# AEAD
2008年之后，业内提出一些新的算法在内部同时实现加密和认证，摒弃了结合二者的路线，这些算法也被称为真正的AEAD算法[@rogaway2002aead]，如AES-CCM和AES-GCM。这么做的一个原因是单一算法实现比简单结合的方式效率更高，但是更重要的原因是上面三种方式都有安全漏洞。

Rogaway2011[@rogaway2011]是一份很好的材料，详细分析了block-cipher的各种模式。
