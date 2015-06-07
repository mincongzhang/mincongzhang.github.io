---
layout: post
title: 优先级队列(Priority queue)
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

### 完全二叉堆

