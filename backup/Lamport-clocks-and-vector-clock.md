---
title: Lamport clocks and vector clocks
tags: distributed system
---

# happen-before relation

����happen-before���£�
1. ����¼�A���¼�B��ͬһ�������ϣ���A��Bǰִ�У���A happen-before B��
2. ����¼�A���¼�B�ڲ�ͬ�����ϣ���A��ʾ���ڽ��̷�����Ϣ��B���ڽ�����һ�¼�����B��ʾ������Ϣ��һ�¼�����A happen-before B��

�Ӷ��岻�ѷ��֣�happen-before��ϵ�д����ԣ���һ����happen-before��ϵ��ƫ���ϵ�������û��A happen-before BҲû��B happen-before A�����A, BΪ�����¼���

��ô���ȷ���ֲ�ʽϵͳ���¼���happen-before��ϵ�أ�����ʱ�Ӳ��ܳе����𣬵�����ʱ�ӿ������䣬�������ʱ�Ӹ���û��ͳһ�޷��Ƚϡ����������������߼�ʱ�����õ������¼�����ʱ�����ܷ���ȷ���¼���happen-before��ϵ��

# Lamport clock

����$P_i$ά���Լ����߼�ʱ��$C_i$���ٶ���ֵ��Ϊ0���ý������¼�A����ʱ����������$C_i$��Ȼ�󽫵�ǰ�߼�ʱ��ֵ��Ϊ���¼���Lamport clock($L(A)$)������ͬһ�������ϵ��¼���Lamport clock��С��ϵ���Ǹý����ϵ�happen-before��ϵ��

���ڿ����������ٶ��¼�A, B�ֱ��ʾ��������$P_i, P_j$�ϵ���Ϣ���ͺͽ����¼���$P_i$������Ϣʱ��Ҫ���Ϸ����¼���Lamport clock($L(A)$)��$P_j$�յ���Ƚ�$L(A)$���Լ����߼�ʱ��$C_j$�����$C_j < L(A)$�������$C_j=L(A)$������ʲô��������֮������$C_j$������B��Lamport clock--$L(B)=C_j$��

��������ͼ��

������Lamport clock�Ķ��壬���ѿ��������A happen-before B����һ����$L(A) < L(B)$������֮�����������ԣ�����Lamport clock�����ǻ��ǲ��ܸ����¼���happen-before��ϵ��

# vector clock

����ʱ�ӣ��ٶ�ϵͳ�й���$n$�����̣���ÿ������ά��һ������${C_i|1 \leq i \leq n}$�����У�$C_i$��Ӧ����$P_i$��

���ڽ���$P_i$�ϵ��¼�A��������ʱ��$VC(A)=[C_1, ..., ++C_i, ..., C_n]$��

���ڽ��̼�Ľ����¼�A, B���¼�A������ʱ��ͬ�ϣ�����ʱ��Lamport clockһ������VC(A)��B�յ���Ƚ�VC(A)��������ʱ�ӵ�ÿ�����������������нϴ��ֵ�������Ӧ������֮��ͬ���������Լ��ķ����������¼�B��Ϊ������ʱ�ӡ�

�������£�

��vector clock���岻�ѿ��������VC(A)�ĸ�������С�ڵ���VC(B)������A happen-before B����֮��Ȼ��

# vector clock in Dynamo

Dynamo��ʹ��vector clock���last write win���µ�д��ʧ����
