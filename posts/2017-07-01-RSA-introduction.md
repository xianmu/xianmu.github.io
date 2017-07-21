---
title: RSA introduction
tags: cryptography
---

1976年，Diffie和Hellman在其著名论文，New Directions in Cryptography[@diffie-hellman1976]中提出了公钥密码学Public-Key Cryptography的概念，并指出PKC需要满足两个需求，一是在不安全信道上完成密钥交换，另一个是要实现电子签名。为满足第一个需求，作者基于离散对数问题提出了后来称为Diffie-Hellman的密钥交换算法。但是，对于第二个问题，他们没能给出一个完整的解决方案。这个重任留给了后来者。

1977年，MIT的Ron Rivest, Adi Shamir, Leonard Adleman三位大神基于大数分解问题提出了RSA算法[@rsa1977]，成为第一个完整的公钥密码学算法，能够同时支持安全密钥传输和电子签名。40年的发展，特别是信息产业的飞速发展，RSA已成为最广泛使用的公钥密码系统之一，电子支付，email，www到处可见其身影。

本文简单介绍RSA相关知识，包括RSA算法理论部分，一些实现细节及优化，最后给出一个性能数据。

# RSA算法
通常，一个密码学系统都至少有三个算法，即\{$KeyGen$, $Enc$, $Dec$\}，其中$KeyGen$用于生成密钥，$Enc$加密明文得到密文，$Dec$解密密文得到明文。RSA的这三个算法具体如下。

## $KeyGen$
首先，$KeyGen$接受一个安全参数$\tau$，输出公私钥对\{PK, SK\}。安全参数$\tau$通常表示着安全强度，在RSA中即公私钥的长度。具体步骤如下：

1. 随机选择两个$\tau$-bit的素数$p$, $q$ ($p \ne q$)，计算$n = pq$;
2. 计算欧拉函数$\phi$(n) = $\phi(p)\phi(q) = (p-1)(q-1)$;
3. 选取一个小于且与$\phi(n)$互素的整数$e$，计算$e$模$\phi(n)$的乘法逆元$d$，即$ed \equiv 1$ mod $\phi(n)$;
4. PK = {$n$, $e$}, SK = {$n$, $d$}。

其中，欧拉函数$\phi(n)$表示小于等于$n$且与$n$互素的正整数个数。对于$n$这样的semiprime(指可以表示为两个素数的乘积的数)，不难想明白$\phi(n)=(p-1)(q-1)$。

## $Enc$
相对于$KeyGen$，$Enc$则要简单很多，它使用公钥PK加密消息$m$，得到密文$c$。
\[ c = m^e\text{ mod }n \]


## $Dec$
解密算法$Dec$也很简单，它使用私钥SK解密密文$c$，得到明文$m$。
\[ m = c^d\text{ mod }n \]

## Correctness
\[ m = Enc(Dec(m)) = m^{ed} \text{ mod } n\]

{Proof}
    由Euler定理有，如果$m$与$n$互素，则$m^{\phi(n)} \equiv 1 \text{ mod } n$，则
    \[ m^{ed} \text{ mod } n = m^{1+k\phi(n)} \text{ mod } n = (m^{\phi(n)})^k m \text{ mod } n = m\]

## Security Reduction
首先，如果我们能快速分解大整数，则显然能够快速求出私钥$d$，从而攻破RSA。反之呢，我们能否把攻破RSA规约到大整数分解素因数问题呢。

# More implementation details

## Big number operations
如前所述，RSA基于大整数的运算，因而实现RSA离不开大数运算库，或者高精度计算库，NIST现在建议的$\tau$取值是$2048$或者$3072$。RSA至少需要大整数的减法，乘法，比较，最大公约数gcd，模乘求逆，模幂，大素数生成等等功能。前几个操作的实现比较简单，gcd通过Euclidean算法实现，模幂运算通过反复平方法可以快速求得，模逆通过Extended-Euclidean算法求得。

## $\tau$-bit primes generation
大素数生成采用重复生成伪随机大整数然后做素性测试直到获得一个素数。

## Speed up with CRT
按上面的描述，公私钥对都不需要大素数$p, q$的配合，$KeyGen$完成就可以销毁它们了。但实际上，具体实现中通常不销毁二者，而是利用中国剩余定理CRT计算几个相关的整数，从而加速解密过程。

## Random padding
以上介绍了基本的RSA算法，但其在实际应用中会有问题。不难发现，以上介绍的RSA算法是一种确定性的加密算法，即对于相同的明文将得到相同的密文。另一方面，结合前面对于攻击模型介绍可知，RSA不能应对选择密文攻击CCA，构造方法很简单。假定有密文：

\[ c = m^e \text{ mod } n\]

构造密文：

\[ c^\prime = 2^e \text{ mod } n\]

选择密文：

\[ C = cc^\prime = (2m)^e \text{ mod } n\]

将选择密文$C$作为输入提供给解密算法即可得出明文$2m$。

为解决这一问题，实际使用的RSA都会对明文先进行random padding，再进行加密。由于需要padding，$\tau$-bit的密钥不能加密$\tau$-bit的明文。同样的，嵌套地加密一个明文，密文会越来越长。以下介绍具体的padding算法。

### PKCS-1.5 padding

### OAEP

# Benchmark
本部分给出rsa算法的性能数据，如下图
