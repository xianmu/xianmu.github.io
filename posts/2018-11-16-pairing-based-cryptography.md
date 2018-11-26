---
title: pairing-based cryptography
tags: cryptography
---

Recently, a lot of attention has been paid on how to construct cryptographic primitives using pairings, like identity-based encrytion[@boneh2001identity], one-round tripartite Diffie-Hellman[@joux2000one], etc. (which will be disscussed in later posts).

However what is pairing? 

Pairing is a map, $e:G_1\times G_2\rightarrow G_T$, where $G_1,G_2$ are both additive cyclic groups of prime order $q$, $G_T$ is another multiplicative cyclic group of order $q$, satisfies the following properties:

Bilinearity:

\[\forall a,b\in F_q^\ast, \forall P\in G_1,Q\in G_2:e(aP, bQ)=e(P,Q)^{ab}\]

Non-degeneracy:

\[e\neq 1\]

Computability:

\[There\ exists\ efficient\ algorithms\ to\ compute\ e.\]

# intractable problems

## BDHP

Bilinear Diffie-Hellman Problem (BDHP) is believed to be intractable, which means it is hard to compute $e(P,P)^{abc}$ given $<P,aP,bP,cP>$, $a,b,c\in \mathbb{Z}_q^\ast$, $P$ is a generator of $\mathbh{G}_1$.

# Weil pairing

In cryptography.

# Tate pairing

Tate pairing is another common used instantiation.
