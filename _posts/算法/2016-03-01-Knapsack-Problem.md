---
layout: post
title: 背包问题(Knapsack Problem)
category: 算法
description: 背包问题(Knapsack Problem)
tags: ["C++","算法"]
---

### 0-1 (0到1)背包问题 (物品可分) (Fractional Knapsack Problem)
1.物品可分: 每个物品可以取任意比例的一部分,按照取的比例获得相应价值
2.算法(贪心): 按照性价比排序,优先选取性价比较高的
3.性价比: 价值/重量(体积)

### 0/1 (0或1)背包问题 (物品不可分) (0/1 Knapsack Problem)
1.物品不可分: 每个物品要么拿走,要么完全不取
2.算法: 枚举？动态规划？

0/1 背包問題是經典的 NP-complete 問題，無法快速求得精確解.只能折衷求得近似解。
然而，當數值範圍不大時，得以用動態規劃快速求得精確解。

3.枚举: 假如有n种物品(每种物品选或者不选2种状态)
O(2^n)复杂度

4.递推:
假设每种物品的价值是v(i),重量是w(i),下标编号1-n. 
背包承重是W,假设A(n,W)是计算最优决策下的物品总价值的算法.

A(n,W) = max(  A(n-1,W), A(n-1,W-w(n)) + v(n)  )

A(n,W)的含义是, 前n件物品,总重量不超过W时的最大价值

4.递推(说人话):
假设每种物品的价值是value(i),重量是weight(i),下标编号1-n. 
背包承重是bag_available_weight,假设A(n,bag_available_weight)是计算最优决策下的物品总价值的算法.

A(n,bag_available_weight) = max(  A(n-1,bag_available_weight), A(n-1,bag_available_weight-weight(n)) + value(n)  )

A(n,bag_available_weight)的含义是, 前n件物品,总重量不超过W时的最大价值

5.动态规划算法
(1)前i件物品,总重量不超过j的最大价值是dp[i][j]  
(2)初值dp[0][*]=0   //前0件物品,总重量无论多少,得到价值都是0

(3)递推
dp[i][j] = 
dp[i-1][j],                                     if(j <  w[i] )
max(  dp[i-1][j], dp[i-1][j-w[i]] + v[i]   ),   if(j >= w[i] )

(4)最优解
dp[n][W]

5.动态规划算法(说人话)
(1)前i件物品,总重量不超过j的最大价值是dp[i][j]  
(2)初值dp[0][*]=0   //前0件物品,总重量无论多少,得到价值都是0

(3)递推
dp[i][j] = 
dp[i-1][j],                                     if(j <  weight[i] )
max(  dp[i-1][j], dp[i-1][j-weight[i]] + v[i]   ),   if(j >= weight[i] )

(4)最优解
dp[n][bag_available_weight]

6.时间复杂度
O(n+1)*(W+1) = O(nW), n个物品,W的可用背包重量

TODO:
Subset sum problem(dynamic programming)
https://en.wikipedia.org/wiki/Subset_sum_problem

Ref:
http://www.csie.ntnu.edu.tw/~u91029/KnapsackProblem.html

