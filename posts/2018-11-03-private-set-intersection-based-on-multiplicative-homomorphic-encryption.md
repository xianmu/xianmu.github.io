---
title: private set intersection based on multiplicative homomorphic encryption
tags: cryptography
---

# the problem

Let's invite our old friends, Alice and Bob. Suppose that they hold a set of elements respectively, and they would like to determine the intersection while keeping the other elements in private from each other. This is the so-called private set intersection(PSI) problem.

A lot of protocols have been proposed to solve PSI, hash-based, GC-based, polynomial interpolation-based, blind signature-based etc. This post explains the solution based on multiplicative homomorphic encryption. (Sorry, I have not found the original paper in literature so far.)

# multiplicative homomorphism

Homomorphism is a property provided by cryptosystems, which means a function of plaintext can be mapped into a function of ciphertext. Take textbook RSA as an example, for two message $m_1, m_2$, we have $E(m_1m_2) = (m_1m_2)^e\ mod\ n = m_1^em_2^e\ mod\ n =E(m_1)E(m_2)$. This is the multiplicative homomorphism of RSA, and this property is very helpful for privacy-preserving computation. The multiplication can be outsourced to cloud without revealing the plaintext $m_1, m_2$.

Additive homomorphism is similar. If a cryptosystem simultaneously holds additive and multiplicative homomorphism, it is fully homomorphic, which is complete for all computable functions theoretically.

# the PSI scheme

In this part, we describe the multiplicative homomorphic scheme for PSI in detail. Firstly, Bob generates his RSA key pair $PK={e,n}, SK={d,n}$ and distributes $PK$ to Alice. Then Alice and Bob execute a secure protocol that determines whether their elements $x$, $y$ are equal.

Let's have a close look on the secure comparison protocol.

Alice------------------------------------Bob

$M_1=E(r)H(x)$------------------------->$M_1$

$N_1$<----------------------------------$N_1=D(M_1)=rD(H(x))$

$N_2$<----------------------------------$N_2=H'(D(H(y)))$

$M_2=H(N_1/r)=H'(D(H(x)))$

$N_2\stackrel{?}{=}M_2$---------------->

$r$ is a random number chose by Alice, $H$ is a cryptographically secure hash function. Apparently, $M_2=N_2$ is equivalent to $x=y$ given that $H$ is collision resistant.

To solve PSI, Alice and Bob need to execute the above protocol for each element pair of their input.

# complexity 

Alice and Bob have to check the equivalence of all the pairs, and this takes $O(n^2)$ runs of the protocol which consumes two round of networking each time.
