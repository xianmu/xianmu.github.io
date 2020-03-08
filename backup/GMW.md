---
title: How To Play Any Mental Game
tags: mutliparty computation
---

GMW[@goldreich1987play]是安全多方计算(SMC)中一个又一个经典协议，由Goldriech, Micali, Wigderson发表于1987年并以他们的名字命名。

与BGW协议不同，GMW基于boolean circuit，但基本思想是类似的，首先将要计算的函数表达成boolean circuit，再计算各个基本门的输出。核心仍然是如何保证在基本门的计算中不泄露信息。

# Share 

与BGW采用Shamir's Secret Sharing不同，GMW采用Additive Secret Sharing，即使得$s=a+b$，其中$a$为随机数。Additive Secret Sharing在恢复secret时要求所有的share，而不能如SSSS那样实现$k$-out-of-$n$的secret恢复。

# Evaluate

然后，GMW协议依次对电路中的XOR($+$)和AND($\times$)门进行计算。

## XOR

XOR比较简单，由于$s_1=a_1+b_1, s_2=a_2+b_2$，Alice和Bob分别将自己持有的share相加得到的新share仍然是结果的share，$s_1+s_2=(a_1+a_2)+(b_1+b_2)$。

## AND

AND复杂些，需要用到$1$-out-of-$4$ OT。假设，Alice获得的结果share是随机值$c_1$，那么Bob如何计算出$c_2=(a_1+b_1)\times (a_2+b_2) - c_1$呢。
