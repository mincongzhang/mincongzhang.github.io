---
layout: post
title: 高级搜索树(Advanced search tree)
category: 算法
description: 链表
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我在edX上Data Structures and Algorithms学习高级搜索树的笔记.

### B树(B-Tree)Intro
1.动机:不同存储级别之间在访问速度上的巨大差异,实现高效的I/O

2."640k ought to be enough for anybody." - B.Gates,1981 这句话略短视,但在B树中是真理

3.越来越大的数据,越来越小的内存:
(1)硬盘容量越来越大,内存显得越来越小
(2)内存无法做太大:储存器容量越大,访问速度越慢
(3)解决方法之一:高速缓存(Cache)

4.高速缓存(Cache):事实1
(1)不同容量的储存器,访问速度差异悬殊
(2)若以此内存访问需要1秒,以此外存访问就相当于1天
(3)为避免1次外存访问,我们宁愿访问内存10次,100次,甚至更多

5.分级I/O
(1)多数存储系统,都是分级组织的--Caching
(2)最常用的数据经可能放在更高层,更小的储存器中
(3)实在找不到,才向更底层,更大的储存器索取

(4)典型的存储系统: CPU( central processing unit/寄存器)-RAM(random-access memory/内存)-DISK(磁盘)-ARRAY(DISK ARRAY/磁盘阵列)-...
(5)容量越来越大,访问速度越来越低
(6)数据在这些不同存储级别转换,都称为输出/输入,I/O
(7)要尽可能减少I/O

6.高速缓存(Cache):事实2
(1)从磁盘中读写1B和读写1KB速度几乎一样
(坐火车去采购粉笔,采购1支和采购10000支时间成本一样)

(2)典型的存储系统采用批量式访问:
以页(page)或块(block)为单位,使用缓冲区//<stdio.h>
(RAM-pages-DISK)

### B树(B-Tree)
1.结构
(1)每个节点可能有多个分叉
(2)所有底层节点的深度完全一致(理想平衡)
(3)显得更宽更矮

2.B-Tree
(1)平衡的多路(multi-way)搜索树
(2)若干个二路节点经适当合并可得*超级节点*

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree.jpg" alt="LCS" title="LCS" height="200"/>
