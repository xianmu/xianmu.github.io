---
title: padding oracle attack
tags: cryptography
---

# Review of CBC mode and padding

CBC is a commonly used confidentiality mode of block cipher, which is demonstrated in the following [picture](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation).

![](/files/CBC.png)

If the last block of plaintext is not complete, we will firstly padding it. The most commonly used padding scheme is PKCS5 padding, which adds $n$ bytes of $n$($n$ is the padding length).

# Padding Oracle Attack

Padding oracle attack was proposed by Serge Vaudenay[@vaudenay2002security] in 2002.

Padding oracle is an abstract model which tells you if the padding is correct.

Let's take a close look at padding oracle attack (the picture is from [Dan Boneh's slides](https://crypto.stanford.edu/~dabo/courses/OnlineCrypto/)).

![PaddingOracleAttack](/files/padding_oracle_attack.PNG)

The ciphertext is $IV||C[0]||C[1]$. To decrypt $C[1]$ without key, we firstly try to determine the last byte of $C[1]$. For each guess $g$, we XOR the last byte with $g\oplus$ 0x01, and feed the forged ciphertext into padding oracle which will tell you whether the padding is valid, if valid, the last byte is exactly $g$, otherwise we try another guess. Once we determine the last byte, we can use two bytes padding "0x02 0x02" to determine the second-to-last byte.

Apparently, a whole block is determined with no more than $256\times 16$ attempts, which is much much less than $2^128$ required to bruteforce the whole block space.
