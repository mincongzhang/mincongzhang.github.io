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

### 背包问题：递归（topdown）和遍历表格（bottomup）方式的对比
1.递归比较容易理解，但是假如递归深度很深，可能出现栈溢出的问题。
2.但是通过实践，其实递归可以减少很多计算量，不需要算出整个表格，即可求解。

3.遍历表格方法复杂度固定，就是O(n×(sum+1))。
4.虽然计算量可能大一些，但是通过优化，可节省内存：不需要n×(sum+1)的表格，提供sum+1的array即可（重复利用覆盖已有的值）。

5.总之，递归减少计算量，遍历减少空间使用。

### 相关问题：子集和问题(Subset sum problem)
1.子集和问题也算是一类背包问题：给出一个数，在一个集合中找到子集，使其相加之和能等于那个数。
2.考察集合内每个数，我们有选取/不选取，两种情况。所以brute force方法就是O(2^n)。
3.用背包问题的思考方式，我们可以建立一个n×(sum+1)的表格。用动态规划解决。
4.参考https://github.com/mincongzhang/Algorithms/tree/master/SubSetSum/



https://en.wikipedia.org/wiki/Subset_sum_problem
http://www.geeksforgeeks.org/dynamic-programming-subset-sum-problem/

Ref:
http://www.csie.ntnu.edu.tw/~u91029/KnapsackProblem.html

