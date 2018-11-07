---
title: blind signature
tags: cryptography
---

# blind signature

Blind signature is a form of digital signature proposed by David Chaum[@chaum1983blind] in 1982. With blind signature, the message keeps private on signing which is of great use in privacy-concerned protocols.

# RSA-based blind signature

In the paper, Chaum only presented an abstract scheme, while this part demonstrates the well-known RSA-based blind signature which is an actual example of it.

![RSA blind signing protocol[@bellare2003one]](/files/rsa_blind_signature.PNG)

# applications

## electronic voting

In electronic voting systems, all ballots should be certified by an election authority, otherwise dishonest elector could forge large numbers of ballots destroying the voting. Privacy is also concerned here because it is undesirable for the voter that anybody else gets to know his/her selection.

![[electronic voting based on blind signature](https://people.cs.nctu.edu.tw/~rjchen/Crypto2010/Blind_Signature.pdf)](/files/electronic_voting_blind_signature.PNG)

With blind signature, the voter could mask his selection with random $r$ and send the masked ballot to the election authority. The authority firstly authenticates the voter, checks whether he has voted, and signs the masked ballot if not. At last, the voter unblinds the signed message and sends it to the voting center who will then check the validity before counting the ballots.

## untraceable digital cash

Similarly, in untraceable digital cash schemes[@chaum1983blind], all digital coins should be originally from the bank while customers would like to preserve the privacy such as when they spend the coins, what they buy etc.

Once again, the customer firstly withdraws enough coins from the bank by generating serial numbers and transmitting the blinded serials to the bank. The bank authenticates the customer, debits his account and signs the blinded serials. On payment, the customer unblinds the signed coins and gives them to the merchant who will then ask the bank for verification and depositing. Apparently, the bank gets no idea of when or what does the customer buy. 

A disadvantage of this centralized digital cash system is that the bank should be always available to prevent double-spending problem. More discussion of digital cash can be found [here](http://www.cs.bham.ac.uk/~mdr/teaching/modules06/netsec/lectures/DigitalCash.html).
