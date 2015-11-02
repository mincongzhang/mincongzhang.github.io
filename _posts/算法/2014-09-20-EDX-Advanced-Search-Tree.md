---
layout: post
title: EDX - 高级搜索树-伸展树(Advanced search tree - Splay Tree)
category: 算法
description: 高级搜索树
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
6.奥妙之处:旋转调整结束后,整棵树的拓扑都变了,树高降低,极端情况(单链)能缩减为一半(如图)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splaytree_zigzag.jpg" alt="LCS" title="LCS" height="200"/>
<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splaytree_result.jpg" alt="LCS" title="LCS" height="200"/>

7.总结:
(1)子孙异侧,v调整到(子)树根;
(2)子孙同侧,反转顺序,例子:g->p->v => v->p->g

8.性能分析
(1)具有折叠效果(修复作用):一旦访问坏节点,对应路径的长度随即减半
(2)单趟伸展作用,分摊O(logn)时间

9.小问题:节点只有父亲没有祖父,只用一次zig或者zag

### 伸展树(Splay Tree): 算法实现

```
template <typename T>
class Splay: public BST<T>{	//由BST派生

protected: BinNodePosi(T) splay(BinNodePosi(T) v);  //将v伸展至根

public:		//伸展树的查找也会引起整树的结构调整,故search()也需要重写
	BinNodePosi(T) & search(const T & e); //查找 (重写,因为查找的时候拓扑有变化)
	BinNodePosi(T) insert(const T & e);	  //插入 (重写)
	bool remove(const T & e);			  //删除 (重写)
}
```

1.伸展算法

```
/*双层伸展*/
//v上升两层,成为(子)树根
template <typename T> BinNodePosi(T) Splay<T>::splay( BinNodePosi(T) v ){
	if (!v) return NULL;
	BinNodePosi(T) p;   //父亲
	BinNodePosi(T) g;	//祖父
	
	while( ( p=v->parent ) && (g=p->parent) ){	//自下而上,反复双层伸展
	//每轮之后,v都将以原曾祖父为父
	BinNodePosi(T) gg = g->parent;
	
	/*四种情况*/
	if( IsLChild(*v) ){
		if( IsLChild(*p) ){/*zig-zig*/}else{/*zig-zag*/}
	} else {
		if( IsRChild(*p) ){/*zag-zag*/}else{/*zag-zig*/}
	}
	
	//若无曾祖父gg,则v现即为树根;否则,gg此后应以v为左或右孩子
	//这时v已经成为(子)树根,v的parent需要更新
	if  (!gg) v->parent = NULL; 
	else(g==gg->lc)? attachAsLChild(gg,v):attachAsRChild(gg,v);
	
	updateHeight(g);updateHeight(p);updateHeight(v);	
	}//双层伸展结束时,必有g==NULL,但p可能非空
	
	if(p=v->parent){/*若p果真是根,只需在额外单旋(至多一次)*/}
	v->parent = NULL;
	return v;//伸展完成,v抵达树根
}
```

2.四种情况

```
/*zigzig*/
if( IsLChild(*v) ){
	if( IsLChild(*p) ){
		/*zig-zig*/
		attachAsLChild(g,p->rc);
		attachAsLChild(p,v->rc);
		attachAsRChild(p,g);
		attachAsRChild(v,p);
	}else{/*zig-zag*/}
} else {
	if( IsRChild(*p) ){/*zag-zag*/}else{/*zag-zig*/}
}
```

3.查找算法

```
template <typename T> BinNodePosi(T) & Splay<T>::search( const T & e ){
	//调用标准BST的内部接口定位目标节点
	BinNodePosi(T) p = searchIn( _root,e,_hot=NULL );
	
	//无论成功与否,最后被访问的节点都将伸展至根(_hot是成功或失败后所在节点的父节点)
	_root = splay(p?p:_hot);//成功or失败
	
	//总是返回根节点,能得到相等或近似(_hot)的节点
	return _root;		//用到了*局部性*
}
```

4.插入算法
(1)直观方法:调用BST标准的插入算法,再将新节点伸展至根(首先需调用BST::search())
(2)但是,重写后的Splay::search()已继承了splay()操作
(3)查找(失败)之后,_hot即是根节点
(4)拆开_hot的一个子树,接上v (如图)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splay_tree_insert.jpg" alt="LCS" title="LCS" height="200"/>

5.删除算法
(1)直观方法:调用BST标准的删除算法,再将_hot伸展至根
(2)但是,Splay::search()查找(成功)后,目标节点即是树根
(3)即可,在查找后的树根附近完成目标节点的删除
(4)之后的连接,一个可行的方法,从右子树中提取最小的节点(参考中序遍历算法迭代版)作为root,连结左子树和剩下的右子树(如图)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree_splay_tree_delete.jpg" alt="LCS" title="LCS" height="200"/>

### 综合评价
优点:
1.无需记录节点高度或平衡因子

2.编程实现简单易行--优于AVL树

3.分摊复杂度O(logn)--与AVL树相当

4.局部性强,缓存命中率极高时(即k<<n<<m)
(1)效率甚至可以更高--自适应的O(logk)
(2)任何连续的m次查找,都可在O(mlogk+nlogn)时间内完成

5.新安装的电脑,经过一段时间的应用后就非常顺手. 是因为通常的操作系统都充分利用了数据访问的局部性,从而使得缓存的命中率能够达到尽可能的高.

缺点:
6.尽管分摊效率好,不能保证杜绝单次最坏情况
(1)伸展树的形状在任何时刻通常都是不平衡,甚至极其不平衡
(2)首次查找将付出代价,尽管之后路径减半

7.对单次操作效率敏感的场合不适用
(1)比如控制手术器械的程序不能采用这一类数据结构



问题:
1.局部性在之前哪里提到了?哪次课?(就在本课最开始的概念Locality)
2.伸展树假设的前提是怎么出现的? 为什么要查找的就要往树根移,要插入和删除的元素也要往树根移动? (比如电脑的使用,数据访问的局部性)

资料补充:
1.自适应链表

参考资料:
1. 伸展树wiki: http://zh.wikipedia.org/wiki/%E4%BC%B8%E5%B1%95%E6%A0%91
2. 相当不错的一个介绍伸展树的博客,还带有其应用场景 http://www.artofproblemsolving.com/blog/54268
