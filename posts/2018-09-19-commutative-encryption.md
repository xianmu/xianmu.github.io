---
title: commutative encryption
tags: cryptography
---

# commutative encryption

An encryption algorithm is commutative if 

\[ E_{k_1}(E_{k_2}(m)) = E_{k_2}(E_{k_1}(m)) \]

Also, we can easily figure out that 

\[ D_{k_1}(D_{k_2}(E_{k_1}(E_{k_2}(m))) = m \]

(Does probabilistic commutative encryption exist? Sorry, I do not know anything about that.)

Most symmetric encryption schemes (such as DES and AES) are not commutative.

# SRA

SRA[@shamir1981mental](stands for Shamir, Rivest and Adleman. Yes, the creators of RSA crypto-system) is a classic communicative encryption. 

Suppose Alice and Bob share two large primes $p, q$ and calculate the same semi-prime $n=pq$. Alice generates her encryption key $e_1$ and decryption key $d_1$ such that $e_1 d_1\equiv 1 (mod \ \phi(n))$. Bob generates his encryption key $e_2$ and decryption key $d_2$ such that $e_2 d_2\equiv 1 (mod \ \phi(n))$. Then decryption with $d_1, d_2$ can be out of the encryption order with $e_1, e_2$. That is the SRA commutative encryption.

SRA is very like RSA except that it is symmmetric as the encryption keys $e_1, e_2$ should be kept private, while RSA is a public-key cryptosystem.

# Pohlig-Hellman exponential cipher

Pohlig-Hellman exponential cipher is another communicative encryption scheme. However, it is also very much like SRA. Remind that SRA uses a composite modulus $n=pq$, while Pohlig-Hellman uses a prime one. Like SRA, both encryption key and decryption key should be kept secret.

The security of Pohlig-Hellman is based on the discrete logarithm problem, so that you should make sure the prime modulus $p$ is large enough, and $\phi(p)=p-1$ have as few small factors as possible(a simple method: choose $p$ such that $(p-1)/2$ is also a prime).

Pohlig-Hellman has very poor performance as the modulus is always very large, such as 2048-bit. In the other hand, it is a deterministic scheme which means it is not semantically-secure(recall that deterministic encryption can never be IND-CPA secure). So that, Pohlig-Hellman is rare in practice.

# mental poker

Mental poker[@shamir1981mental] asks that can two dishonest players play a fair game online without trusted third party.

Take the following example:

Alice and Bob want to play mental poker,

1. Both Alice and Bob hold 5 hand randomly choosed from a deck of cards (totally 52 cards) provided one of them is honest

2. Neither Alice nor Bob can control what cards they each will get

3. None of them can capture any information about the other one's hand

SRA descripted above is the first solution targeting this problem in 1980.

Beyond the encryption and decryption keys, suppose that Alice and Bob share dinstinct messages $M_1, M_2, ..., M_52$ denote the deck of cards.

1. Alice encrypts the deck, $C_i=E_{k_1}(M_i), 1 \leq i \leq 52$, randomly permutes it and sends the ciphertexts to Bob

2. Bob randomly picks 5 as Alice's hand and sends it back to Alice who can decrypt it

3. Bob randomly picks another 5 as as his own hand, to get the plain cards, Bob encrypts the ciphertext with his own encryption key and sends them to Alice who will decrypt the double ciphered message and send the partial decrypted cards back again. Finally, Bob decrypts the ciphertext and get his hand cards.

Because SRA is commutative, they both will get the right cards. In the other hand, since SRA is deterministic, Alice and Bob can easily find dishonest duplicate cards.
