---
title: pairing-based cryptography
tags: cryptography
---

Pairing is a new algebra structure of great use in cryptography.

What is pairing? Pairing is a map, $e:G_1\times G_2\rightarrow G_T$, where $G_1,G_2$ are both additive cyclic groups of prime order $q$, $G_T$ is another multiplicative cyclic group of order $q$. A pairing should also satisfy the following properties:

Bilinearity:

\[\forall a,b\in F_q^\ast, \forall P\in G_1,Q\in G_2:e(aP, bQ)=e(P,Q)^{ab}\]

Non-degeneracy:

\[e\neq 1\]

Computability:

There exists efficient algorithms to compute $e$.
