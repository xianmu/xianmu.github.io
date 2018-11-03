---
title: private set intersection based on multiplicative homomorphic encryption
tags: cryptography
---

# the problem

Let's invite our old friends, Alice and Bob. Suppose that they hold a set of elements respectively, and they would like to determine the intersection while keeping the other elements in private from each other. This is the so-called private set intersection(PSI) problem.

A lot of protocols have been proposed to solve PSI, hash-based, GC-based, polynomial interpolation-based, blind signature-based etc. This post explains the solution based on multiplicative homomorphic encryption. (Sorry, I have not found the original paper in literature so far.)

# multiplicative homomorphism

Homomorphism is a property provided by cryptosystems, which means a function of plaintext can be mapped into a function of ciphertext. Take textbook RSA as an example. For RSA, we have $E(m_1\mul m_2) = (m_1m_2)^e mod n = m_1^em_2^e=E(m_1)\mul E(m_2)$. This is the multiplicative homomorphism of textbook RSA, and this property is very helpful for privacy-preserving computation. The multiplication can be outsourced to a cloud without revealing the plaintext $m_1, m_2$.

Additive homomorphism is similar. If a cryptosystem simultaneously holds additive and multiplicative homomorphism, it is fully homomorphic, which is complete for all computable functions theoretically.

# the PSI scheme

