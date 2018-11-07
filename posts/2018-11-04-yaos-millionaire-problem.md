---
title: Yao's millionaire problem
tags: cryptography
---

# Yao's millionaire problem

In 1982, Andrew Yao proposed the millionaire problem, which discusses how could two millionaires determine which of them is richer while keeping their actual wealth private.

# secure multiparty computation

Secure multiparty computation(SMC, or multiparty computation-MPC, privacy-preserving computation, secure computation, secure function evaluation-SFE) is an abstract of this kind of problem. SMC asks for protocols that enable several parties collaboratively compute a function without exposing their input.

More specifically, a set of parties, $P_1, P_2, ...,P_n$, each of whom has a input $x_i, 1\leq i \leq n$, they want to compute $y=f(x_1, x_2, ..., x_n)$ while keeping $x_i$ in private. Yao's millionaire problem is SMC with comparison as $f$.
