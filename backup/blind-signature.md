---
title: blind signature
tags: cryptography
---

# blind signature

# RSA blind signature

$m'=mr^e\ mod\ n$

blind signature, $Sign(m')={m'}^d\ mod\ n=m^dr\ mod\ n$

unblinded signature, $Sign(m)=Sign(m')r^{-1}\ mod\ n$
