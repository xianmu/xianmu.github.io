---
title: square root of a
tags: algorithm
---

一道经典面试题，如何求$a$的平方根($sqrt(a)$)，答案是迭代下面公式直到误差达到要求。

\[x_{n+1}=\frac{1}{2}(x_n+\frac{a}{x_n})\]

这个公式如何得到的呢，答曰，通过牛顿迭代法得出。

# Newton's method

牛顿迭代法用于求函数的零点，如上面的平方根例子，即求$x$满足$x^2-a=0$。
