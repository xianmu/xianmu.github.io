---
title: communication complexity of locating the median between two parties 
tags: communication complexity
---

# Problem

假设Alice和Bob分别拥有一个集合$x,y\subset {1,...,n}$，$MED(x,y)$表示$x\cup y$的中位数。问求$MED(x,y)$的通信复杂度是多少？所谓通信复杂度指完成计算需要传输的bit数。

# Naive method

Trivial的办法是Alice或者Bob将自己的集合发给对方，复杂度为$O(n)$，由另一方计算完成后将结果回传，回传结果需要$O(logn)$，整体的复杂度$O(n)$。

# Optimize

