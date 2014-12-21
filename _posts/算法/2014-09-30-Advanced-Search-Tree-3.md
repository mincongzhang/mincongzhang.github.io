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

### 所以红黑树到底是怎么使用,从而可以来表示历史版本?
*变化量不会超过O(1)是什么意思? 只有一次3+4操作
*红黑树实现了"任何一次动态操作引发的结构变化量不会超过O(1)"之后,要怎么玩?

  
### 红黑树(Red–black Tree) 结构
1.红黑树历史
  symmetric binary B-tree 对称二叉树
  red-black tree 红黑树
  half-balanced binary search tree 半平衡二叉树
  
2.定义
  由红黑两类节点组成的BST
  (统一增设外部节点NULL,使之成为*真二叉树* 
   真二叉树 - 每个内部节点都有两个孩子,尽管孩子有可能是新增的外部节点NULL)
   
  概括:
  (1)树根        :必为黑色
  (2)外部节点    :均为黑色
  (3)其余节点    :若为红,则只能有黑孩子 (红之子,之父必黑)
  (4)外部节点到根:途中黑节点数目相等    (黑深度)
  
3.提升变换(lifting)
(1)将每一个红色的节点提升至于其父亲平齐
(2)可观察到底层节点平齐
(3)黑节点与其红孩子视作(关键码,然后合并为)超级节点
(4)无非四种组合:黑(1个节点2个分支), 黑红, 红黑, 红黑红(3个节点4个分支)
(5)红黑树 == (2,4)树 B树 

4.平衡性
(1)由等价性,既然B树是平衡的,红黑树自然也是
(2)B树的高度 == 黑节点的总数 (红黑树的黑高度)

5.接口定义

```
/*RedBlack*/

template <typename T> class RedBlack : public BST<T> { //红黑树
  public: 
    //BST::search()等其余接口可直接沿用
	BinNodePosi(T) insert( const T & e ); //插入(重写)
	bool remove( const T & e );   		  //删除(重写)
	
  protected: //内部接口
    void solveDoubleRed( BinNodePosi(T) x );  //双红修正
    void solveDoubleBlack( BinNodePosi(T) x );//双黑修正
	int updateHeight( BinNodePosi(T) x );	  //更新节点x的高度(不是常规的高度,是黑高度)
};

template <typename T> int RedBlack<T>::updateHeight( BinNodePosi(T) x ){
	x->height = max( stature(x->lc),stature(x->rc) );
	if(IsBlack(x)) x->height++; 
	
	return x->height;//只计黑节点
}

```


### 红黑树(Red–black Tree) 插入(insert)
1.双红缺陷(double red)
  (1)拟插入关键码e
  (2)按BST的常规算法插入
  (3)将x染红,条件1,2,4满足,考虑条件3
  (4)有可能双红(如图)
  (5)x的祖父必然存在且一定是黑的,重点是其叔父
  (6)针对*叔父*的颜色分两种情况处理

```
/*红黑树条件*/
  (1)树根        :必为黑色
  (2)外部节点    :均为黑色
  (3)其余节点    :若为红,则只能有黑孩子 (红之子,之父必黑)
  (4)外部节点到根:途中黑节点数目相等    (黑深度)
```
  
<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_red_problem.jpg" height="200"/>
  
2.算法框架

```
template <typename T> BinNodePosi(T) RedBlack<T>::insert(const T & e){
	//确认目标节点不存在(留意对_hot的设置)
	BinNodePosi(T) & x = search(e); if(x) return x;
	
	//创建红节点x,以_hot为父,黑高度-1
	x = new BinNode<T>(e,_hot,NULL,NULL,-1);
	_size++;
	
	//感觉这里新建节点的时候也没有标明它的颜色啊?颜色信息存在哪?
	
	//如有必要,需做双红修正
	solveDoubleRed(x);
	
	//返回插入的节点
	return x?x:_hot->parent;   //这里什么时候会返回_hot的parent?它parent是啥?

}//无论原树中是否存在e,返回时总有x->data == e
```

3.第一种情况:叔父节点u是黑色
(1)直接提升(lift)
(2)参照AVL树,做局部3+4重构
(3)重新染色,中间染黑,左右染红
  
<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_red_problem2.jpg" height="200"/>
  
(4)双红缺陷调整前非法的原因:
   -某个三叉节点中插入红关键码,使得原黑关键码不再居中(RRB或者BRR)
(5)调整后的效果:
   -新的四叉节点中,三个关键码的颜色改为RBR
(6)拓扑结构的调整O(1)
   -一蹴而就(3+4),无需进一步调整
 
<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_red_problem3.jpg" height="200"/>

4.第二种情况:叔父节点u是红色
(1)提升变换(lift)
(2)超级节点发生上溢(overflow) (此时,修复红黑树的双红缺陷 == 修复B树的上溢缺陷)
(3)居中(偏右)的关键码上移并插入父节点
(4)从红黑树的角度看,就是节点颜色改变了

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_red_problem4.jpg" height="200"/>

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_red_problem5.jpg" height="200"/>

(5)父节点由于插入了红色节点,继续recursive解决
(6)从B树的角度看,拓扑结构改变
(7)从红黑树的角度,只是节点颜色改变
  (染色次数变化可能logn,拓扑结构变化O(1))

5.归纳
(1)复杂度分析
   -重构,染色均属常数时间的局部操作,只需统计其总次数
   -每次插入,可在O(logn)时间内完成
   -最多做:O(logn)次颜色染色;1次3+4重构
(2)重构操作对持久化结构(persistent structures)很重要

### 红黑树(Red–black Tree) 删除(delete)
1.更复杂,可利用提升(lifting)转换成B树看待,并且反过来理解过程
2.依然关注重构操作,重构(reconstruction)次数都不超过常数O(1)

3.算法框架

```
/*红黑树条件*/
  (1)树根        :必为黑色
  (2)外部节点    :均为黑色
  (3)其余节点    :若为红,则只能有黑孩子 (红之子,之父必黑)
  (4)外部节点到根:途中黑节点数目相等    (黑深度)
```

(1)首先按照BST常规算法,执行:
r = removeAt( x,_hot ); (删除了在_hot以下的节点x)

(2)如果返回r,表示remove之后x由孩子r接替(如小标题4.下的图)
   另一个孩子记作w(即黑的NULL)

(3)这时红黑树原则1,2依然满足

(4)但3和4不一定:
   违背原则3:有可能出现两个连续的红色节点
   违背原则4:若删除的是黑节点,途中黑节点数目可能不相等,如小标题4.下图的(b)

4.若二者之一为红
(1)3,4不难满足,如下图的(a)(b)
(2)解决方法:将替代者r染黑即可(等于删除了一条虚边,外部节点黑深度保持原状)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_delete_structure.jpg" height="200"/>

5.双黑缺陷(double-black)
(1)若x与r均黑,则是双黑缺陷,全树黑深度不统一,违反原则4
(2)从B树角度看,x所属节点下溢(underflow)
(3)考察两个节点:
   -r的父亲: p = r->parent (也就是原树中x的父亲)
   -r的兄弟: s = r==p->lc? p->rc : p->lc

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-3_RedBlackTree_double_black_problem.jpg" height="200"/>





算法导论对R-B Tree的介绍：
红黑树，一种二叉查找树，但在每个节点上增加一个存储位表示节点的颜色，可以是Red或Black。 通过对任何一条从根到叶子的路径上各个节点着色方式的限制，红黑树确保没有一条路径会比其他路径长出俩倍，因而是接近平衡的。