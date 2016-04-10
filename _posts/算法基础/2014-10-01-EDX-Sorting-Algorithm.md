---
layout: post
title: EDX - 排序算法总结
category: 算法基础
description: 图
tags: ["C++","算法"]
---

整理edX上Data Structures and Algorithms里所有排序算法.

### 快速排序(quick sort)
1.分而治之
(1)将序列分为两个子序列: S = S_l + S_r
(2)规模缩小
(3)彼此独立 max(S_l) <= min(S_r)
(4)在子序列分别__递归__排序后,原序列自然有序:sorted(S) = sorted(S_l) +　sorted(S_r)
(5)平凡解:只剩单个元素时,本身就是解
(6)mergesort的计算量和难点在于__合__,quicksort在于__分__

2.轴点
(1)pivot:左侧比它小,右侧比它大
(2)以轴点为界,原序列的划分[low,high) = [low,mid) + [mid] + (mid,high)
(3)于是现在问题就成了快速找轴点,之后就可以递归求解

```
template <typename T>
void vector<T>::quickSort(Rank lo,Rank hi){
	if(hi-lo < 2)  return;
	
	Rank mi = partition(lo,hi-1);

	quickSort(lo,mi);
	quickSort(mi+1,hi);
}
```

(4)坏消息:在原始序列中,轴点未必存在
(5)必要条件:轴点必定已然就位
(6)特别地:在有序序列中,所有元素皆为轴点
(7)快速排序:就是将所有元素逐个转换为轴点的过程
(8)好消息:通过适当交换,可使任一元素转换为轴点

3.构造轴点
(1)经典步骤,选取第一个元素作为m
(2)然后lo在左,hi在右,慢慢对比往中间移动

4.性能分析
(1)不稳定:重复?
(2)最好情况:每次取轴点都接近平均,轴点接近中央, O(nlogn)
(3)最坏情况:每次划分都不平均,轴点总是min/max, O(n^2)
(4)即使采用随机选取,三者取中(随机抽3个,选取居中者)之类的策略,也只能降低最坏情况概率,无法杜绝
(5)平均性能:仍然是O(nlogn)

### 冒泡排序(bubble sort)
1.

### 归并排序(merge sort)
1.


### 选择排序(selection sort)

```
/*selection sort*/
//对列表中起始于位置p的连续n个元素做选择排序,valid(p) && rank(p) + n <= size

tamplate <typename T> void List<T>::selectionSort(Posi(T) p,int n){
	//待排区间(head,tail)
	Posi(T) head = p->pred;
	Posi(T) tail = p;
	for(int i=0; i<n; i++){ 	//head/tail可能是header/trailer.
		tail = tail->succ; 		//(tail到trailer的succ了怎么办? 岂不是null了?)	
		/*改进方法:
		if(tail->succ != NULL){
			tail = tail->succ;
		}
		*/
	}
	
	while(n>1){
		insertBefore(tail,remove( selectMax(head->succ,n) ));//insert要new,remove要delete
		tail = tail->pred;									 //是通常基本操作的100倍
		n--;												 //改进(1):直接修改reference
	}														 //改进(2):修改succ/pred
}
```

```
/*selectMax*/

template <typename T>
Posi(T) List<T>::selectMax(Posi(T) p, int n){
	Posi(T) max = p;
	
	for(Posi(T) cur=p;n>1;n--){
		if( !lt((cur = cur->succ)->data,max->data) ) //not less than
			max = cur;								 //painter's algorithm
	}												 //这里有一个trick, 要用>=,不用>
													 //selectMax()中对于多个相等的最大元素，选取其中位置最靠后者
	return max;
}
```

1. 分析:
(1)复杂度O(n^2)
(2)但是元素移动操作远少于起泡排序
(3)O(n^2)来自于元素*比较*
(4)第10章selectMax()可以在O(logn)而不是O(n)时间内完成

### 插入排序 (Insertion Sort)

1.将序列看成两部分
(1)sorted + unsorted

2.我的想法, 插入只需要对比O(logn)次,(binary compare?); 但是,在list里没办法call-by-rank

3.实现

```
/*Insertion Sort*/
//对列表中起始于位置p的连续n个元素做插入排序

template<typename T>
void List<T>::insertSort(Posi(T) p,int n){
	for(int r=0;r<n;r++){		//r是已排序的元素个数
		insertAfter( search(p->data,r,p),p->data );		
		p = p->succ;
		remove(p->pred);		//转向下一节点
	}//O(r+1)
}//仅用O(1)辅助空间,就地算法(in-place algorithm)
```

4.性能分析
(1)最好情况,几乎完全有序,O(n);最坏情况,O(n^2); 但平均也是O(n^2)
(2)selection所有情况都是O(n^2),没有最好最坏

5.后向分析法(backward analysis)
[0+1+...+(n-1)]/2+1 = O(n^2)

6.逆序对 (inversion)
(1)左大右小,构成inversion
(2)inversion记录在右(小)的那个元素
(3)逆序对数量可能n^2
(4)统计每一个元素所对应的inversion有多少个,累计能得到整个序列所含inversion的数目
(5)p所对应的inversion有多少个,就要经过多少次比较才能抵达最终插入位置
(6)最好情况,逆序对总数0,复杂度O(0+n)
(7)最坏情况,逆序对总数n^2,复杂度O(n^2+n)=O(n^2)


### 桶/计数排序(Bucket sort/Counting sort)
1.算法性能不再完全取决于输入的规模,也就是序列的长度n; 同时也取决于序列元素的取值范围,记作M(用来表示过散列表的长度)

2.渐进时间复杂度:O(M+n), 或者O(max(M,n))

3.待排序元素的取值范围越有限,算法的优势越明显

4.英文大写字母的排序(存在大量重复):
(1)表长M取作26,遍历输入集,统计出现次数就行了(和我3D搜索里发明的rasterization算法一样)
(2)以上这一步骤也称作分配:distribution;还有一个accumulation是累计值(积分)
(3)图像处理里的histogram equalization也用到了
(4)通过累计值accumulation可得到输出序列中对应区间范围

### Radix Sort
http://en.wikipedia.org/wiki/Radix_sort


### Heap Sort

### Tournament Sort

### 排序算法对比

<iframe width="560" height="315" src="//www.youtube.com/embed/fZkqGaPuYPA?rel=0" frameborder="0" allowfullscreen></iframe>
