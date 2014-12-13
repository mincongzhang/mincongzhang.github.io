---
layout: post
title: 高级搜索树2-B树 (Advanced search tree 2 - B-Tree)
category: 算法
description: 高级搜索树
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
以页(page)或块(block)为单位,使用缓冲区(<stdio.h>...)
(RAM<-pages->DISK)

### B树(B-Tree)
1.结构
(1)每个节点可能有多个分叉
(2)所有底层节点的深度完全一致(理想平衡)
(3)显得更宽更矮

2.B-Tree
(1)平衡的多路(multi-way)搜索树
(2)若干个二路节点经适当合并可得*超级节点* (如图2代合并得4路分支)

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree.jpg"  height="200"/>

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
	-一般节点:ceil(m/2) <= n+1
	-树根节点:2 <= n+1 (此后理解和回答,在"插入-分裂"出解答)
(3)故亦称作(ceil(m/2),m)-树
(4)总结:保证内部节点至少半满来最小化空间浪费

*(x,y)树就是,每个节点最少x个分支最多y个分支

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

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_BTNode.jpg"  height="200"/>

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

2.B树:查找

```
/*B-Tree:Search*/
//诀窍:只载入必需的节点,尽可能减少I/O操作
//假设根节点常驻于内存,其他存在于外存之中

template <typename T> BTNodePosi(T) BTree<T>::search( const T & e ){
    BTNodePosi(T) v = _root; 
	_hot = NULL;
	
	while( v ){ //逐层查找
	
		//在当前节点对应的向量中顺序查找(调用vector的查找接口)
		//Vector<T> key;  //关键码,数值向量
		Rank r = v->key.search(e); 
		
		if ( 0<=r && e == v->key[ r ] ) return v; //成功则返回
		
		//不成功的话,向量查找r=key.search(e)得到的是不大于目标关键码的最大值
		_hot = v;
		//沿引用转至对应的下层子树(右孩子),并载入其根I/O
		v = v->child[ r+1 ];
		
	}//若因!v而退出,则意味着抵达外部节点
	
	return NULL; //失败
}
```

3.B树:查找 - 复杂度分析
(1)同一层的vector的查找,使用的是顺序查找而不是二分查找;
   原因:每一个节点的大小大概是kb级的,所含关键码大概100个,实验结果表明二分查找在此类中效率更低

(2)影响效率的因素是树的高度h
   -下限(每个节点包含尽可能少的关键码):
   相对于BBST: log(m/2,N/2)/log(2,N) = 1/(log(2,m)-1)
   若取m=256,树高(I/O)次数约降低至1/7
   -上限(每个节点包含尽可能多的关键码):
   相对于BBST: (log(m,N)-1)/log(2,N) = 1/log(2,m)
   若取m=256,树高(I/O)次数约降低至1/8

4.B树:插入

```
/*B-Tree:Search*/

template <typename T>
bool BTree<T>::insert( const T & e ){

	BTNodePosi(T) v = search(e);//这里会返回_hot
	if(v) return false;//确认e不存在
	
	Rank r = _hot->key.search(e);//在节点_hot中确定插入位置
	_hot->key.insert(r+1,e);     //紧随其后插入
	//关键部分!分支需要增加,增加在插入关键码的右孩子,
	//但是节点是位于底层的叶节点,所以直接在分支vector最后增加一个NULL
	_hot->child.insert(r+2,NULL);//创建一个空子树指针
	
	_size++;
	
	//如发生上溢(overflow),需做*分裂*
	//可能下属分支增加,导致分支总数超过B树阶次m
	solveOverflow(_hot);
	
	return true;//插入成功

}

```

5.B树:插入-分裂
(1)分裂方法如下图:
节点的[下界,上界] = [ceil(m/2),m]

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree_split_when_insert.jpg" height="200"/>

(2)问题:父节点中又插入了新的节点(有可能上溢)
   解决方法:向上传播,继续分裂,直到根节点
   
(3)分裂到根:根节点已经没有父节点,提升的那个关键码作为父节点(根节点)
   由于分裂,这时根节点只有两个分支<=ceil(m/2)
   
(4)复杂度:O(1)*h = O(h)

6.B树:删除

```
template <typename T>
bool BTree<T>::remove( const T & e ){

	BTNodePosi(T) v = search(e);
	if(!v) return false;//确认e的存在
	
	//确定e在v中的秩(位置),
	//如果v是叶节点,child=null,那么r就是e所在的位置
	//如果v不是叶节点(有child),那么r就是是不大于目标关键码的最大值
	Rank r = v->key.search(e);
	
	if(v->child[0]){ //若v不是叶节点(有child),
	
		BTNodePosi(T) u = v->child[r+1];//在右子树中一直向左
		
		//找到e的后继(succ)(必属于某叶节点)
		while( u->child[0] ) u = u->child[0];
		
		//交换
		v->key[r] = u->key[0];
		v = u;//将u所在的Posi赋给v,之后方便删除其中的第0个值
		r = 0;//交换后e在0的位置    
	}
	
	//至此,经过交换,v必然位于最底层,且其中第r个关键码就是待删除者
	v->key.remove(r);
	v->child.remove(r+1);//删除对应的分支(其实child都是null,可删除任何一个)
	_size--;
	
	solveUnderflow(v);//可能突破下限,称为下溢
	return true; //如果有必要,需做旋转或合并
}
```

7.B树:删除 - 旋转
(1)节点v下溢时,
   必恰好包含:ceil(m/2)-2个关键码  (B树约定至少ceil(m/2)-1个关键码)
   以及:      ceil(m/2)-1个分支    (B树约定至少ceil(m/2)个分支)

(2)解决方法1:旋转
   左/右兄弟(sibling)节点的关键码(key)数量 >= [ceil(m/2)-1]+1 = ceil(m/2)
   从兄弟节点借出一个关键码,但需要从父节点处旋转
   (保证搜索树的性质,左子树<=父节点<=右子树)
   如图:
   
   <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree_delete_and_rotate.jpg" height="200"/>

(3)解决方法2:合并
   左/右兄弟(sibling)不存在,或所含关键码均不足ceil(m/2)个
   如图,将父节点关键码取出,并且与两个子节点合并
   不能直接合并两个子节点,因为这样就违背了搜索树顺序排列的原则
   父节点关键码合并之后,相当于删除了一个关键码,需要往上一层解决删除问题
   
   <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法/Advanced-Search-Tree-2_multiway_search_tree_delete_and_merge.jpg" height="200"/>
   
   
   

### 参考资料
1.从B 树、B+ 树、B* 树谈到R 树:http://blog.csdn.net/v_july_v/article/details/6530142