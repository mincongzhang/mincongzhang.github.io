---
layout: post
title: 高级搜索树(Advanced search tree)
category: 算法
description: 链表
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我在edX上Data Structures and Algorithms学习高级搜索树的笔记.

### 伸展树(Splay Tree)
1.宽松平衡: 比AVL树更宽松

2.局部性(Locality)
刚被访问过的数据,极有可能很快地再次被访问.

3.在BST中的局部性(Locality)
刚刚被访问过的节点,极有可能很快地再次被访问.
下一将要访问的节点,极有可能就在刚被访问过节点的附近.

4.问题
连续的m次查找(m>>n=|BST|),采用AVL共需O(mlogn)时间.
利用局部性能否更快? //仿效自适应链表

****自适应链表是什么?

5.自适应调整
(1)以list为例,对任何一个元素的访问效率,取决于其秩(rank)
(2)rank越小,访问效率越高,反之效率低
(3)如果对集合的访问具有局部性,可高效访问
(4)一旦有某个元素刚被访问,将其移动到序列最前端

6.对BST也进行这种自适应调整(self-adaption?)

### 伸展树(Splay Tree): 逐层伸展
1.节点v一旦被访问,随即转移至树根
2.转移: 利用此前的等价变换,就是zig和zag
3.节点逐层上升的过程是一个左右摇摆不断伸展的过程,所以称为伸展(splay)

4.最坏情况:树退化成了一个list,第一个节点复杂度O(n),第二个O(n-1),第三个O(n-2)...
整个周期长度为n,整个计算成本是O(n^2),分摊到单个操作是O(n),和普通list的访问复杂度一样了.(AVL树是O
(logn)). 

*稍作调整就能发挥强大功力=>双层伸展

### 伸展树(Splay Tree): 双层伸展
1.Tarjan: Self-Adjusting Binary Trees
2.构思精髓: 向上追溯两层,而非一层
3.反复考察祖孙三代:孙节点一次性上升两层

4.子孙异侧:旋转方式和AVL树的双旋调整没区别
5.子孙同侧:越级,祖父节点先旋转,再旋转父节点,孙节点就能上升2层(如图)(一般情况,父节点旋转,祖父节点旋转,孙节点上升两层)
6.奥妙之处:旋转调整结束后,整棵树的拓扑都变了,树高降低,极端情况能缩减为一半(如图)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splaytree.jpg" alt="LCS" title="LCS" height="200"/>
<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splaytree_result.jpg" alt="LCS" title="LCS" height="200"/>

****AVL树里的zig和zag的实现还需要再看看

### 伸展树(Splay Tree): 算法实现


资料补充:
1.自适应链表

参考资料:
1. 伸展树wiki: http://zh.wikipedia.org/wiki/%E4%BC%B8%E5%B1%95%E6%A0%91