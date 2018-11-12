---
title: Identity based cryptography
tags: cryptography
---

In 1984, Adi Shamir proposed a new cryptographic primitive, identity-based encryption(IBE)[ref]. However, Shamir only gave a construction of identity-based signature(IBS) rather than IBE which remained open for many years. In 2001, Boneh-Franklin[] and Cock[] seperately proposed their schemes of IBE.

# motivation

As everyone knows, PKC(public key cryptography) solved the problem of symmetric key distribution, and PKI(public key infrastructure) solved the problem of public key distribution by introducing TTP(trusted-third party), CA for instance. Then TTP of PKI has to store large volume of certificates and the clients should acquire server's certification before authentication. 

While within IBE, the receiver's ID is just his public key, such that no additional resource or extra round of communication is needed for the certificates.

Since IDs can be random strings, IBE allows a random string as public key, which is dissatisfied in tranditional PKC (RSA, Elgamal, ECC, etc.).

# Boneh-Franklin IBE

In this part, we are going to describe the Boneh-Franklin IBE scheme. BF-IBE involves three roles as the following figure describes.

![ID-based encryption [from wiki](https://en.wikipedia.org/wiki/ID-based_encryption)](/files/Identity_Based_Encryption_Steps.png)

Alice, the sender would like to transmit some message to the receiver, Bob, under the protection of BF-IBE. A thirdparty named PKG(Private Key Generator) is introduced, and it is critical to the whole scheme.

The algorithms:

1. $Setup(\tau)$.

$\qquad$Step 1. Generate an admissible bilinear map $\hat{e}:G_1\times G_1\rightarrow G_2$, $G_1, G_2$ of prime order $q$, and choose a random generator $P\in G_1$.

Step 2. Pick a random $s\in \mathbb{Z}_q$

2. Extract.

3. Encrypt.

4. Decrypt.
