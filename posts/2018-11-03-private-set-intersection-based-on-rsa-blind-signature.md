---
title: private set intersection based on rsa blind signature
tags: cryptography
---

# the problem

Let's invite our old friends, Alice and Bob. Suppose that they hold a set of elements respectively, and they would like to determine the intersection while keeping the other elements in private from each other. This is the so-called private set intersection(PSI) problem.

# blind signature-based PSI scheme

A lot of protocols have been proposed to solve the PSI problem, hash-based, GC-based, polynomial interpolation-based, etc. This post explains the solution[@de2010practical] based on blind signature discussed in a previous post. 

Firstly, Bob generates his RSA key pair $PK=\{e,n\}, SK=\{d,n\}$ and distributes $PK$ to Alice. The protocol is depicted in the following figure.

![Blind RSA-based PSI Protocol with linear complexity[@de2010practical]](/files/rsa_blind_signature_psi.PNG)

$c_i(1\leq i\leq \upsilon)$ are elements hold by client, $s_j(1\leq j\leq \omega)$are the server's, $H, H'$ are cryptographic hash functions. In step 2, client blinds her input getting $y_i$ which will then be signed by server in step 4, and in step 6, client unblinds the signature. Easy to find that, $t_i'=H'(D(H(c_i))), t_j=H'(D(H(s_j)))$, and $c_i=s_j$ if $t_i'=t_j$ given that $H,H'$ are collision-resistent and $D$ is PRP.
