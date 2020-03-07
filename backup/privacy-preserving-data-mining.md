---
title: privacy preserving data mining
tags: cryptography
---

本文是Privacy Preserving Data Mining[]一文的阅读总结，该文针对ID3算法提出了一种保护隐私的方法。

ID3作为一种经典的决策树算法，其基本思想是递归地将数据集按信息增益最大的属性划分成子树最终形成一棵决策树，不算复杂，这里就不作过多介绍了。

记数据集$T$，属性集$R$，类别集$C$。当前数据集的信息熵为

\[ H_C(T) = \sum_{i=1}^l-\frac{|T(c_i)|}{|T|}log\frac{|T(c_i)|}{|T|} \]

按属性$A$划分的信息熵

\[ H_C(T|A) = \sum_{j=1}^m\frac{|T(a_i)|}{|T|}H_C(T(a_j)) \]

信息增益为$Gain(A)=H_C(T)-H_C(T|A)$.

