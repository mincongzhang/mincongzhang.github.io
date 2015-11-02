---
layout: post
title: EDX - 优先级队列(Priority queue)
category: 算法
description: 优先级队列(Priority queue)
tags: ["C++","算法","数据结构"]
---

这篇文章记录了我在edX上Data Structures and Algorithms学习优先级队列(Priority queue)的笔记.

### 应用需求
1.访问次序不是FIFO,而是带有优先级

2.例子:夜间门诊,系统多任务调度交由CPU处理,哈夫曼编码树取出权重最小的两颗子树

### 优先级队列(Priority queue)接口

```
template <typename T>
struct PQ {   //Priority queue
	virtual void insert(T) = 0; //按照优先级次序插入词条
	virtual T getMax() = 0;     //取出优先级最高的词条
	virtual T delMax() = 0;		//删除优先级最高的词条
}; 

// virtual XX()=0是纯虚函数,强制要求具体实现

//与其说PQ是数据结构,不如说是ADT(抽象数据类型Abstract Data Type);
//其不同的实现方式,效率及适用场景也各不相同
//Stack和Queue,都是PQ的特例 -- 优先级完全取决于元素的插入次序(LIFO,FIFO)
```

### 基本实现
1.向量Vector, 删除略麻烦,得把后面的元素都前移
2.有序向量Sorted Vector
3.列表List同理不适用
4.有序列表Sorted List同上

5.BBST:
(1)AVL,Splay,Red-Black,三个接口均只需要O(logn)时间
(2)但是BBST却过于强大,远超出需求,因为只需要getMax和delMax,不需要完全遍历搜索.
(3)若只需查找极值元素,不必维护所有元素之间的全序关系,偏序足矣

### 完全二叉堆(Complete Binary Heap):结构
1.完全二叉树(Complete Binary Tree):是平衡因子处处非负的AVL树
2.结构性:
(1)逻辑上,等同于完全二叉树
(2)物理上,直接借助向量实现
3.就是按照层次遍历(level traverse)平铺成的vector

```
#define Parent(i) ( (i-1)>>1 )  //(i-1)/2
#define LChild(i) ( 1+(i<<1) )  //1 + i*2 奇数
#define RChild(i) ( (1+i)<<1 )  //(1+i)*2 偶数
```

4.融合

```
//PQ_ComplHeap = PQ + Vector

typedef int Rank;

template <typename T>
class PQ_complHeap : public PQ<T>, public Vector<T>{

protected:
	Rank precolateDown( Rank n, Rank i );  //下滤  //precolate 滤,渗透
	Rank percolateUp  ( Rank i );		   //上滤
	void heapify      ( Rank n );          //Floyd建堆算法

public:
	PQ_ComplHeap( T* A, Rank n ){	//批量构造
		copyFrom( A, 0, n );
		heapify( n );
	}
	
	void insert( T );			//按照比较器确定的优先级次序,插入词条
	T getMax(){ return _elem[0]; }	//读取优先级最高的词条
	T delMax();  					//删除优先级最高的词条
```

5.堆序性
(1)任何节点,在数值上,都不会超过它的父亲
if( 0<i )  H[i] <= H[ Parent(i) ]
(2)故H[0]即为全局最大元素

```
PQ_ComplHeap<T>::getMax(){ return _elen[0]; }
```

### 完全二叉堆(Complete Binary Heap):插入与上滤
1.为插入词条e,只需将e作为末元素接入向量
(1)结构性自然保持
(2)若堆序性也亦未破坏,则完成(但是未必)
(3)若堆序性被破坏, 只能是e与其父节点违反堆序性, e与其父节点换位, (若堆序性因此恢复,则完成,否则再来一次)

2.以上过程,亦所谓上滤(percolate up)(lets call it "move up" =_=)

3.实现

```
template <typename T> void PQ_ComplHeap<T>::insert( T e ){  //插入
	Vector<T>::insert( e );
	percolateUp( _size-1 );
}

template <typename T>  //对第i个词条实施上滤, i<_size
Rank PQ_ComplHeap<T>::percolateUp( Rank i ){
	while( ParentValid(i) ){				//只要i有父亲,尚未抵达堆顶,则
		Rank j = Parent(i);					//将i之父记作j
		if( lt(_elem[i],_elem[j]) ) 		//_elem[i]<_elem[j]
			break;							//一旦父子不再逆序,上滤即完成
		swap( _elem[i],_elem[j] );			//否则,交换父子位置,上升一层
		i = j;
	}
	
	return i;//返回上滤最终抵达的位置
}
```

4.效率
(1)完全二叉树是理想平衡二叉树,所以所需时间就是level的层数, 或者按二叉搜索反向看,就是O(logn)
(2)常系数还能改进:
-swap有三次赋值操作,所以就3*logn
-将插入词条e作备份,直到找到最终要替换的节点再swap
-最终3*logn变成(logn+2),2是两次赋值操作

### 完全二叉堆(Complete Binary Heap):删除与下滤
1.只能获取或删除最大元素
2.getMax()只是常数的时间O(1)

3.delMax():下滤
(1)摘除向量首元素,代之以末元素e(保持结构性,破坏堆序性)
(2)e与孩子中的*大者*交换(这样大的才能上去)
(3)一直进行下去

4.实现

```
template <typename T>
PQ_CompHeap<T>::delMax(){
	T max_elem = _elem[0];
	_elem[0] = _elem[--_size]; //末词条和首词条替换
	percolateDown(_size,0);	   //对新堆顶实施下滤
	return max_elem;	       //返回备份的最大词条
}

//原视频代码感觉不好用,且提到的宏没有给出,以下给出改进版

template <typename T>	//对前n个词条中的第i个实施下滤, i<n
Rank PQ_ComplHeap<T>::percolateDown( Rank n,Rank i ){

	Rank j=getLargerChild(_elem,n,i);	//should be current max child, if _elem[i] smaller that its children?
	while( i != j ){
		swap(_elem[i],_elem[j]);
		i = j;
		j=getLargerChild(_elem,n,i);
	}
	
	return i;
}
```

5.效率
(1)只有比较和交换
(2)总体不会超过O(logn)
(3)交换:同理交换仍然可以改进
(4)比较:有本质的区别:
-上滤只用和单一父节点比较
-下滤要和两个孩子对比,要做两次比较
-(在多叉堆中差异更大)
