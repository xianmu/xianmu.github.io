---
title: coin flipping by telephone a protocol for solving impossible problems
tags: cryptography
---

所谓coin flipping by telephone[@blum1983coin]指这样一个问题，假设Alice和Bob两口子刚离婚不久并已在不同城市居住，为公平起见他们决定通过抛硬币的方式决定车应该归谁。他们既不想见面，也不想借助于第三方来裁决，如何通过一部电话完成这件事呢。

显然，这个理想中的协议需要满足两个限制，一是结果要随机；二是不能允许任何一方操纵结果或者说作弊。

在介绍Blum protocol[@blum1983coin]前，需要先给出以下概念，Normally Secure One-Way Function即普通的单向函数，正向计算很简单，反向计算较困难。Completely Secure One-Way Function在Normally Secure One-Way Function的基础上还要求$f(x)$不暴露$x$的任何信息，如奇偶性。

基于completely secure 1-1 one-way function($f$)，有如下协议：

1. Alice randomly selects $x$ unknown to Bob;

2. Alice sends $f(x)$ to Bob;

3. Bob tells Alice whether he thinks $x$ is even or odd;

4. Alice tells Bob if he guessed right or wrong and sends $x$ to convince him.

由于completely secure 1-1 one-way functions不一定存在，文章中基于normally secure one-way function构造来一个completely secure one-way function。该单向函数$f$满足$f(x)=f(y),x\neq y, lsb(x)\neq lsb(y)$，具体构造如下：

\[ f(x) = x^2 \text{mod} n\]

其中，$n=pq$为一个足够大的semi-prime，在协议开始前由Bob生成并发送给Alice，对$p,q$的选择有一定要求，而且必须能由Alice检测这一条件，否则Bob可以通过选择特殊的$n$进行作弊。
