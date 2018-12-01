---
title: Shamir's secret sharing scheme
tags: cryptography
---

Secret sharing是一类能够实现将一个secret分成$n$个share，并且只有当至少有$k$个share时才能恢复该secret。Secret sharing在安全多方计算(SMC)中有着重要应用。

Shamir's secre sharing scheme(ssss)是secret sharing的一个具体实现方案，它利用多项式的特性及拉格朗日插值实现share的生成及secret的恢复，并能提供information theoretic security。

# polynomial

对于$k-1$次多项式：

\[f(x)=a_0 + a_1x + a_2x^2 + ... + a_{k-1}x^{k-1}\]

若已知所有系数$\{a_i|0\leq i\leq k-1\}$，可以计算其在任意点的函数值$(x, f(x))$。另一方面，只要知道该多项式上的$k$个点，则可以计算出全部的系数，也可以计算任意其它点的值($k$个点唯一确定一条$k-1$次曲线，代入联立方程组用高斯消元法可得)。

## Lagrange Interpolation

实际上，为了计算$f(x)$在其它点的值，无需先求所有系数，可以直接利用拉格朗日插值求任意$f(x)$。

假定，已知$f(x)$上的$k$个不同的点$\{(x_i,y_i))|1\leq i\leq k\}$，则

\[f(x)=\sum_{i=1}^{k}y_il_i(x)\]

\[l_i(x)=\prod_{1\leq j\leq k,j\neq i}\frac{x-x_j}{x_i-x_j}\]

其中$l_i(x)$称为拉格朗日插值基函数(证明略)。

# Shamir's Secret Sharing Scheme

Shamir's Secret Sharing Scheme(SSSS)先选一条随机的曲线(在域$Z_p$上取$k-1$个随机值作为系数)，并设置其常数项为secret($s$)。

\[f(x)=\mathbf{s} + a_1x + a_2x^2 + ... + a_{k-1}x^{k-1}\ mod\ p\]

在该曲线上取$n$个点$\{(x_i,f(x_i))|1\leq i\leq n\}$作为share，由拉格朗日插值，只要有其中$k$个点的值即可计算$f(0)=s$。

# Update

SSSS可以进行横向或者纵向更新，横向更新指增加新的share，纵向更新指刷新原来的share但是保持secret不变。横向更新较简单，利用拉格朗日插值计算曲线在新的$x$处的函数值即可。

纵向更新则先选一条新的常数项为0的随机曲线

\[g(x)=\quad b_1x + b_2x^2 + ... + b_{k-1}x^{k-1}\ mod\ p\]

计算其在各个share的处的值$(x_i,g(x_i))$，并加到原来各个share上，由于$g(x)$常数项为0，所以不改变secret，但将曲线更新为

\[h(x)=f(x)+g(x)=s + (a_1+b_1)x + (a_2+b_2)x^2 + ... + (a_{k-1}+b_{k-1})x^{k-1}\ mod\ p\]
