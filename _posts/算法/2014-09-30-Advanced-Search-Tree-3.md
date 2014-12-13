---
layout: post
title: 高级搜索树3-红黑树 (Advanced search tree 3 - Red–black Tree)
category: 算法
description: 高级搜索树
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我在edX上Data Structures and Algorithms学习高级搜索树的笔记.

### 红黑树(Red–black Tree) 动机
1.Ephemeral data structure (暂时性结构)
  以前学的数据结构改变状态后,会遗忘以前的结构状态; 都是随时变换的
  
2.Persistent structure (一致性结构/持久性结构)
  支持对历史版本的访问,需要版本号
  T.search(ver,key); 
  T.insert(ver,key);
  T.remove(ver,key);
  
3.蛮力实现:每个版本独立保存;各版本入口自成一个搜索结构
  将历史快照的数目记作h(history):
  单次操作:O(logh + logn) 
  时间空间:O(h*n)  时间上,因为每一次修改,都要重新复制并修改;空间上,每次历史h都要保存
  挑战    :可否将复杂度控制在O(n+h*logn)内?
  结论    :可以,利用相邻版本之间的关联性 
  
4.关联性
(1)大量共享,少量更新:每个版本的新增复杂度,仅为O(logn)
(2)进一步提高,总体O(n+h),单版本O(1)

*更复杂的数据结构在 邓俊辉-计算几何 中

5.O(1)重构
(1)树形结构的拓扑,相邻版本之间的差异不能超过O(1)
(2)拓扑结构差异来自旋转操作(节点的父节点改变),
   AVL树为例,插入旋转O(1),删除可能会逐层向上引发O(logn)的旋转,导致拓扑结构剧烈变化
(3)所以我们需要一种BBST,任何一次动态操作引发的结构变化量不会超过O(1) -- 红黑树
  
### 红黑树(Red–black Tree) 结构
1.红黑树历史
  symmetric binary B-tree 对称二叉树
  red-black tree 红黑树
  half-balanced binary search tree 半平衡二叉树
