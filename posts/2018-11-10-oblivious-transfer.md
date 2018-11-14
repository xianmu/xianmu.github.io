---
title: oblivious transfer
tags: cryptography
---

Oblivious transfer(OT) is a crypto primitive which let Alice transfer one of many messages to Bob, such that the transfered message remains oblivious to Alice and the other messages keeps private from Bob.

# 1-2 oblivious transfer

Suppose Alice possesses two message $m_0, m_1$, Bob wishes to receive one of them satisfying the privacy constraints, Bob gets no information of the other message and Alice does not know which is transmitted to Bob.

Denote Alice's RSA key pair as $PK=\{e, N\}, SK=\{d, N\}$, $m_b$($b\in \{0, 1\}$) is the message Bob wishes to receive, $i\in \{0, 1\}$.

The following table demonstrates the protocol by Even, Goldreich and Lempel[@even1985randomized], which is from [wiki](https://en.wikipedia.org/wiki/Oblivious_transfer).

![EGL OT protocol](/files/egl_ot_protocol.PNG)

Actually, $m_i'-k=m_i+((x_b+k^e)-x_i)^d-k$, if $i=b$, $m_i'-k=m_b$.

The EGL protocol can be easily generalized to solve 1-out-of-$n$ oblivious transfer.
