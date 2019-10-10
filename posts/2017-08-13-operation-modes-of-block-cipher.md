---
title: operation modes of block cipher
tags: cryptography
---

总结常用的block cipher operation modes，本篇只包括五种基本的加密mode(ECB, CBC, CTR, CFB, OFB). 文献[@rogaway2011evaluation]比较全面地总结了block cipher的各种operation mode，包括加密，MAC，认证加密的。

# 结论

先上结论。

|  mode |  安全 |  padding |  iv/ctr |  并行加密|  并行解密 | EP|code-size|max-enc-length|
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |------------ |------------ |
|   ECB|   ×|   √|   ×|   √|   √|  1|大| |
|   CBC|   √|   √|   iv|   ×|   √|  2|大| |
|   CTR|   √|   ×|   ctr|   √|   √|  1|小| |
|   CFB|   √|   ×|   iv|   ×|   √|  2|小| |
|   OFB|   √|   ×|   iv|   ×|   ×|  2|小| |

1. EP指ErrorPropagation，密文单block出错影响几个block解密。
2. code-size，一些mode只依赖于block-cipher加密算法，代码体积更小，适合嵌入式设备。
3. 安全指CPA-secure，非AE做不到CCA-secure。
4. 性能，在单线程上，这几种mode没有本质差异。多线程上显然可并行的可以更快。

# operation modes of block cipher

为什么需要operation mode？
block cipher顾名思义，加解密单位是一个block，但应用层需要加密的明文长度可能是任意的，如何基于单block加解密的算法构造加密任意长度明文的加解密算法即operation mode所要解决的问题。

## ECB

ECB是naive/trivial的做法，如下图(以下图片全部来自[wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation "wiki"))。

![](/files/ECB.PNG)

由图可知，ECB加解密直接将明文/密文按block-size分成若干block，分别对这些block进行加解密，结果拼接成明/密文。由于各个block的加解密互不相关，所以加解密的可并行性也是显然的。

很简单，对不对，但是有如下两个问题。

### padding

明文可能不是block-size的整数倍，最后一个block长度不够。为了使用block-cipher加密，需要先padding。常见padding方法可参照[链接](https://cryptosys.net/pki/manpki/pki_paddingschemes.html "链接")，其中最常用的是前两种。

1. PKCS5 padding: 0xb个0xb, 比如最后一个block差3个Byte，就填充3个3，差8个就填充8个8；

2. OneAndZero padding: 填充0x8000...，即填充一个1其余全部补0；

如果明文刚好是block-size的整数倍，则要padding一个完整的block。上面两个padding方法相比，PKCS5 padding由于指明了padding-size，所以unpad的时候直接去掉尾部几个字节就可以了，1-0 padding则需要反向顺序找到0x80才能unpad。

#### ciphertext stealing

[ciphertext stealing](https://en.wikipedia.org/wiki/Ciphertext_stealing)， 密文窃取，是一种避免padding的方法，如下图所示：

![](/files/ciphertext_stealing.png)

即将倒数第二个block密文的一部分挪给最后一个block，凑成一个block-size后加密，挪过来的那部分在倒数第二个block里面也不必再存，最后这个block解密的时候可以还原。经过密文窃取后，最后一个block解密失败是会导致倒数第二个block也解密失败的。图中最后两个block在密文中还交换了下位置，这个是为了对齐。对ECB而言如果明文不足一个block显然没法使用这个办法。

### insecurity

ECB是不安全的(should never be used!!!), ECB由于没有随机性，对于相同的明文总是得到相同的密文，这暴露了明文的相等性模式。下例来自[wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation "wiki")

![](/files/ECB_insecurity.png)

如图，最左为要加密的明文，假定该图片是一幅灰度图，每个像素点用一个block表示灰度值。ECB加密后得到中间的图，由于ECB是确定性加密(DET, DET不可能是CPA-secure), 相同灰度的像素点加密后仍然有相同的灰度值。尽管我们不知道秘钥是什么，也不知道完整明文是什么，但是这里还是泄漏了明文的信息，ECB不安全性一目了然。所以什么是安全呢？语义安全性要求密文不泄漏任何明文信息。最右图是比较理想的加密结果。

## CBC

CBC是应用最为广泛的mode，加解密图解如下：

![](/files/CBC.png)

上图加密，下图解密。对比ECB，几个显著不同，IV; XOR; chaining。XOR没什么可说的，由于其性质，经常被用做masking。

IV(InitialVector)必须是unpredictable的，通常使用随机数(或者CSPRNG生成的伪随机数)。IV无需保密，一般放在密文开头，一些服务为了节省带宽会由c/s两端协商同一个算法生成，需要C/S两端维护并同步这个状态。

CBC的加密不可并行，解密可并行，密文某个block解密失败会导致下一个block解密也失败，都可以清晰的从图中看出。由于chaining，如果明文有变动，其后续所有的block的密文都需要重新生成。

CBC也有padding的问题，方法与ECB一样，ciphertext stealing中不足一个block时可以steal IV的一部分。

## CTR

CTR是另一种较常用的mode，加解密图解如下：

![](/files/CTR.png)

与CBC不同，CTR里面没有chaining，block cipher的输入是counter值，而CBC中依赖于前一个block的密文；

第二点不同，加解密都可以并行，并且block cipher的输出可以预计算，因为其不依赖于明密文；

第三点不同，不需要block cipher的解密算法(对所有PRF有效，不局限于PRP)，code-size可以更小；

第四点不同，CTR无需padding，按最后一个block的长度取block cipher等长输出XOR后作为密文。

counter值不同于IV，IV要求unpredictable，而counter要求unique(nonce)。使用随机值作为counter值，在加密2^64次后重复概率较高，使用counter就没有这个问题。

## CFB

CFB mode如下图所示：

![](/files/CFB.png)

与CBC非常类似，差别是CBC先与前一个block的密文异或，再用block cipher加密；CFB先用block cipher加密后与明文异或。由于后异或，所以CFB也不需要padding。

### self-synchronization

利用移位寄存器(shift register)，可以对任意block长度应用CFB mode，如[下图](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.95.21&rep=rep1&type=pdf)所示：

![](/files/cfb_self_sync.PNG)

并且，如果传输密文丢失几个block，待对应的几个block移出移位寄存器后，CFB重新开始正常解密，这被称为CFB的自同步(self-synchronization)特性。

## OFB

OFB mode如下图所示：

![](/files/OFB.png)

与CFB只有一点点不同，CFB中block cipher加密的是前一个block的密文，OFB中block cipher加密的是前一个block cipher的输出，少一个与明文的异或。由于解密依赖前一个block cipher的输出而非密文，所以OFB解密不可以并行。OFB可看做是一种stream cipher，由IV和key生成随机bit流，然后与明文/密文异或(OFB的加密和解密算法完全一样)，随机bit流可以预计算。OFB自同步与CFB相同。
