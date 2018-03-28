---
title: partition problem
tags: algorithm
---

# partition problem

Number partitioning指这样一个问题，对一个给定的集合$S$，判定是否能个将其划分成两个元素和相等的子集。例如，有集合$S={1,1,1,2,2,3}$，其子集$S_1={1,1,1,2}, S_2={2,3}$二者和相等。这样的解可能不存在也可能不唯一，集合$S={2,5}$没有解，$S_1={1,1,3}, S_2={1,2,2}$是上例的另一个解。

## algorithms

### dynamic programming

基于动态规划的思想，可以设计一个伪多项式的算法，如下：


### approximation approaches

除了基于动态规划的伪多项式算法外，还有一些启发式方法能够给出近似最优解，它们通常有更低的复杂性。

基于贪心的思想有如下近似算法：


另一个启发式算法是Karmarkar-Karp's differencing algorithm：

# subset sum problem

子集和问题即对于给定的集合$S$及元素$s$，是否存在$S$的一个子集的和等于$s$。不难看出，上述划分问题是子集和问题的一个特例，取$s=Sum(S)/2$。

# bin packing problem

Bin packing是一个相关的问题，假定你有一堆需要打包的物品以及打包的箱子，每个箱子有固定的容积$V$，求将所有物品打包的最小的箱子数，当然前提是不能超过每个箱子的容积。

# multiway partition problem

Multiway partition是partition problem的泛化，即划分成$k$个子集，使得每个子集的和尽可能接近。

# partition problem under specific distribution

这个问题是我提的，目前还没有找到文献。主要想法是考虑在集合服从特定分布的情况下，有没有比较好的划分算法。从实践中来说，通常集合$S$都是服从一些特定分布的，典型的如power law等等。长尾分布对于互联网应用是十分常见的，考虑你要将所有用户的数据分配给$k$个进程处理，如何产生一个尽可能均衡的划分？或者，在这样的特定输入下，现有的近似算法，哪种效果比较好呢？
