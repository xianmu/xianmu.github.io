---
title: Yao's millionaire problem
tags: cryptography
---

In 1982, Andrew Yao proposed the millionaire problem[@yao82millionaire], which discussed how could two millionaires determine who is richer while keeping their actual wealth private.

# secure multiparty computation

Secure multiparty computation(SMC), or secure computation, secure function evaluation(SFE) is an abstract of this kind of problems. SMC asks for protocols that enable several parties collaboratively compute a function without exposing their input.

More specifically, a set of parties, $P_1, P_2, ...,P_n$, each of whom has a input $x_i, 1\leq i \leq n$, they want to evaluate $y=f(x_1, x_2, ..., x_n)$ while keeping $x_i$ in private. Yao's millionaire problem is SMC with comparison as $f$.

# homomorphic encryption based solution

Lots of solutions have been proposed in literature to solve the millionaire problem. We only discusses the homomorphic encryption based solution proposed by Lin and Tzeng[@lin2005efficient].
