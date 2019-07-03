---
title: square root
tags: algorithm
---

一道经典面试题，如何求$a$的平方根($sqrt(a)$)，答案是迭代下面公式直到误差达到要求。

\[x_{n+1}=\frac{1}{2}(x_n+\frac{a}{x_n})\]

这个公式如何得到的呢，答曰，通过牛顿迭代法得出。

# Newton's method

牛顿迭代法用于求可导函数$y=g(x)$的零点，如上面的平方根例子，即求$x$满足$g(x)=x^2-a=0$。实际上$y=g(x)$的零点是$f(x)=x-\frac{g(x)}{g'(x)}$的不动点，代入$g(x)=x^2-a$即可得上面的平方根迭代公式。

下图展示了牛顿迭代法的过程(来自wiki[https://en.wikipedia.org/wiki/Newton%27s_method])。

![Newton's method](/files/NewtonIteration.gif)

由图，每次迭代，牛顿迭代法首先计算该点$x_n$处的切线$y=g'(x_n)(x-x_n)+g(x_n)$与$x$轴的交点，即$x_{n+1}=x_n-\frac{g(x_n)}{g'(x_n)}$。
