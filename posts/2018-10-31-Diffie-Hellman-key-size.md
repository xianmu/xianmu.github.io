---
title: Diffie-Hellman key size
tags: cryptography
---

[Diffie-Hellman key exchange protocol](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) has two key sizes, the discrete log group size and the discrete log key size. More specifically, the discrete log group size is the length of the modulo $p$, while the discrete log key size is the length of the exponents, $a$ or $b$, chosen by Alice or Bob.

Currently in 2018, the recommended value of the two sizes is 256-bit for key size and 2048-bit (always the same as the recommended key length of factoring based crypto-system, RSA for instance) for group size. (The latest recommended key length can be found in [KeyLength](https://www.keylength.com/))
