---
title: password authentication with insecure communication
tags: security
---

This post is a reading notes of Lamport81[@lamport1981password], better to check out the origin paper for more details.

# the problem

A password-based authentication system could be attacked by adversaries in three ways, 

1. cracking the server's file system;

2. evasdropping the channel;

3. deceiving the user to disclose his/her password.

Among these vulnerabilities, the 3rd one cannot be prevented thoroughly by the protocol, so that Lamport81[@lamport1981password] mainly focused on the other two weaknesses.

# the solution

Easily to figure out the key constraints, 

1. the server can complete the authentication with the stored and transmitted values;

2. neither stored nor transmitted information could disclose the password;

3. the transmitted value cannot be deduced from the stored and previous ones.

The server-side weakness can be eliminated by storing the hashed value of password. To defend the evasdropping, we should never repeat the transmitted message. One method is to use different password for each session, but how to distribute these one-time passwords.

1. initially storing a bunch of hashed passwords in the server;

2. negotiating the next value during current session.

Both of the two methods are imperfect, the former requires more space, and the latter is not robust considering the communication failure or even interference from attacker. 

Lamport81[@lamport1981password] presented a brilliant scheme. Suppose the initial hashed value stored by the server is $h^{1000}(x)$, in the $i$-th authentication, the client sends $h^{1000-i}(x)$ which will be hashed for another time and verified with the stored value. After the verification, the server stores the received value $h^{1000-i}(x)$ for next session.

For the $i$-th authentication, the messages could be collected include $h^{j}(x), 1000-i+1 \leq j \leq 1000$, however the value to be transmitted, $h^{1000-i}(x)$, is difficult to be deduced from the disclosed informations given that $h$ is cryptographically secure.

This method has an important robustness property against crash attack. Suppose that the server restarts before flushing the novel hash value into disk, the client has to transmit the previous message for a second time which may have been recorded by the evasdropper. With Lamport81[@lamport1981password], the server can accept subsequent passwords by repeating hashing and verification until success, and the client needs not to send a used value.

# practical considerations

1. How to choose a new password when the limit 1000 is reached.

2. Security, the attacker can hijack all the communication and impersonate the client with hijacked messages.

3. Efficiency, client generates hash value each time, maybe he can precompute all the values. What proper value should the limit choose.

4. The server has to refresh the stored value every time.

5. The client has to get a memory of what values have been transmitted even after his death and reborn.

6. Privacy, the attacker can recognize the client, this may not be a problem.

