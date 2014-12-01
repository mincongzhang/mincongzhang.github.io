---
layout: post
title: 高级搜索树2:B树 (Advanced search tree 2: B-Tree)
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
(2)若干个二路节点经适当合并可得*超级节点* (如图2代合并得4路分支)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree.jpg" alt="LCS" title="LCS" height="200"/>

3.多路(multi-way)有什么用呢
(1)*多级*储存系统中使用B树,可针对外部查找,大大较少IO次数
(2)充分利用外存对*批量访问*的高效支持,将此特点转化为优点
(3)每下降一层,都以*超级节点*为单位,读入*一组*关键码

(4)具体多大一组?视磁盘的数据块大小而定,m = #keys / pg
(5)例子
	目前多数数据库系统采用 m=200~300, 若有1G(10^9)个记录
	若取超级节点的规模m = 256,则每次查找只需log(256,10^9)<=4 次I/O
	
4.深度(depth)统一
(1)m阶B-Tree,即m路平衡搜索树(m>=2)
(2)所有叶节点(internal nodes:真实存在的末端节点)的深度统一相等
(3)外部节点(external nodes:叶节点的那些数值为空其实并不存在的孩子)的深度统一相等
(4)B树的高度h = 外部节点(而不是叶节点)的深度

5.阶次m含义
(1)内部节点的上限:
	-不超过m-1(或n)个关键码
	-不超过m(或n+1)个分支
(2)内部节点的下限:
	-分支数n+1也不能太少
	-一般节点:上整[m/2] <= n+1
	-树根节点:2 <= n+1 (此后理解和回答)
(3)故亦称作(上整[m/2],m)-树
(4)总结:保证内部节点至少半满来最小化空间浪费
*上整:ceil(x)

(4)例子
	-m=5,(3,5)-树
	-m=6,(3,6)-树
	-....(4,7)...
	-....(4,8)...
	-m=4,(2,4)-树(特殊,与红黑树有特殊的渊源)


### 维基上清楚的介绍了B树运用的理念
1.保持键值有序，以顺序遍历
2.使用层次化的索引来最小化磁盘读取
3.使用不完全填充的块来加速插入和删除
4.通过优雅的遍历算法来保持索引平衡
5.B树通过保证内部节点至少半满来最小化空间浪费。一棵B树可以处理任意数目的插入和删除。

### 代码实现
1.B树的节点类

```
/* BTNode */

template <typename T> struct BTNode { //B树节点
	BTNodePosi(T) parent; //父
	
	Vector<T> key;  	  			//关键码,数值向量
	Vector< BTNodePosi(T) > child;	//孩子向量(长度总比key多一)
	
	BTNode(){parent = NULL; child.insert(0,NULL);}
	BTNode(T e, BTNodePosi(T) lc=NULL, BTNodePosi(T) rc=NULL){
		//作为根节点,而且初始时仅一个关键码,以及两个孩子
		parent = NULL;	
		key.insert(0,e);
		child.insert(0,lc);
		child.insert(1,rc);
		
		if(lc) lc->parent = this;
		if(rc) rc->parent = this;
	}
}
```

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_BTNode.jpg" alt="LCS" title="LCS" height="200"/>

```
/* BTree */

#define BTNodePosi(T) BTNode<T>*	//B树节点位置

template <typename T> class BTree{	//B树
	int _size;	//关键码总数
	int _order;	//阶次
	BTNodePosi(T) _root; //根
	BTNodePosi(T) _hot;	 //search()最后访问的非空节点位置
	
	void solveOverFlow( BTNodePosi(T) );	//因插入而上溢后的分裂处理
	void solveUnderFlow( BTNodePosi(T) );	//因珊瑚而下溢后的合并处理
	
	public:
		BTNodePosi(T) search( const T & e );//查找
		bool insert( const T & e );			//插入
		bool remove( const T & e );			//删除
};

```

### 参考资料
1.从B 树、B+ 树、B* 树谈到R 树:http://blog.csdn.net/v_july_v/article/details/6530142