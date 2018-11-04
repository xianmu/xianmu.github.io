---
title: Identity based cryptography
tags: cryptography
---

In 1984, Adi Shamir proposed a new cryptographic primitive, identity-based encryption(IBE)[ref]. However, Shamir only gave a construction of identity-based signature(IBS) rather than IBE which remained open for many years. In 2001, Boneh-Franklin[] and Cock[] seperately proposed their schemes of IBE.

# motivation

As everyone knows, PKC(public key cryptography) solved the problem of symmetric key distribution, and PKI(public key infrastructure) solved the problem of public key distribution by introducing TTP(trusted-third party), CA for instance. Then TTP of PKI has to store large volume of certificates and the clients should acquire server's certification before authentication. 

While with IBE, the receiver's ID is just his public key, such that no additional resource or extra round of communication is needed for the certificates.

Since IDs can be random strings, IBE allows a random string as public key, which is dissatisfied in tranditional PKC (RSA, Elgamal, ECC, etc.).

# Boneh-Franklin IBE

In this part, we are going to describe the Boneh-Franklin IBE scheme.
