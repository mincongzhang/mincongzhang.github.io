---
layout: post
title: EDX - 动态规划(Dynamic Programming)
category: 算法基础
description: 动态规划
tags: ["C++","算法"]
---

这篇文章记录了我学习动态规划找到的一些有用的资料以及edX上清华Data Structures第一章的笔记.

### Fibonacci
1. 递归(Recursion)

```
int fib(int n){
	return (n<2)?n:(fib(n-1)+fib(n-2));
}
//复杂度:O(2^n)
//空间:O(n)
```

2. 记忆(memoization)
(1)将已计算过实例的结果制表备查
(2)ref:http://en.wikipedia.org/wiki/Memoization

3. 动态规划(Dynamic Programming)
(1)颠倒计算方向:由自顶而下递归,为自底而上迭代

```
int fib(int n){
	int f=0; //fib(0)
	int g=1; //fib(1)
	while(0<n--){
		g=g+f;
		f=g-f;
	}
	return g;
}
//复杂度:O(n)
//空间:O(1)
```

```
//自己感觉更易懂的写法
int fib(int n){
	int f=0; //fib(0)
	int g=1; //fib(1)
	int prev_f;
	while(n-- > 0){
		prev_f = f;
		f=g;
		g=g+prev_f;
	}
	return g;
}
//复杂度:O(n)
//空间:O(1)
```

### 最长公共子序列(Longest common subsequence problem,LCS)

1. 子序列(subsequence):由序列中若干字符,按原相对次序构成
e.g.
tsinghua子序列sina, computer子序列cute

2. 最长公共子序列(Longest common subsequence):两个序列公共子序列中的最长者
e.g.
edudational 和 advantage: data/dana (可能有多个)
didactical 和 advantage: data (可能有歧义,第一个词did...)

3.分析和理解
(1)暂时不管计算效率,先考虑一个可行的LCS算法,用递归
(2)给出两个序列A,B, 从末端看, 有三种情况
-某个序列是空, return
-俩序列末端相同,return LCS(A(end-1),B(end-1))+A[end]
-俩序列末端不同,return max(  LCS(A(end-1),B(end)),LCS(A(end),B(end-1))  )

4. 递归(recursion)解法
对于序列A[0,n]和B[0,m]LCS(A,B),三种情况
(1)n=-1或者m=-1, 
取作空序列("")											//可作为递归基(base)
(2)A[n]='X'=B[m],
取作LCS(A[0,n-1],B[0,m-1])+'X'							//减而治之decrease and concour
(3)A[n]!=B[m],
在LCS(A[0,n],B[0,m-1])和LCS(A[0,n-1],B[0,m])取更长者	//分而治之devide and concour

分析:
最好情况(不出现(2)的情况),只需要O(n+m)
出现(2)的情况,就像Fibonacci那样出现雷同,当n=m时,复杂度O(2^n)
子问题总共不过O(n+m)种
所以采用dynamic programming策略, 只需O(n*m)时间

```
#include <iostream>
#include <string>

#define log(msg) do{std::cout<<msg<<std::endl;}while(0)
std::string LCS(std::string & a, int a_end, std::string & b, int b_end){
  if(a_end<0 || b_end<0) return "";

  if(a[a_end] == b[b_end]){
    return LCS(a,a_end-1,b,b_end-1) + a[a_end];
  } else {
    std::string a_lcs = LCS(a,a_end,b,b_end-1);
    std::string b_lcs = LCS(a,a_end-1,b,b_end);
    return (a_lcs.size()>b_lcs.size())? a_lcs : b_lcs;
  }
}

int main(){
  log("LCS:");

  //xyz
  std::string a="abxcaybzcabc";
  std::string b="dxefydefzdef";
  std::string lcs = LCS(a,a.size()-1,b,b.size()-1);
  log(lcs);
}
```

4. 递归(recursion)的缺点
(1)重复计算: 在计算到下一个相同位置(a[a_end] == b[b_end])的时候, 最坏可能经历n^2次递归

5. Dynamic Programming解法  
(1)制表  
(2)分而治之,取左上+1           (就是a[end] == b[end]的情况,序列数+1)   
(3)减而治之,看上,左,取最大的复制 (就是看LCS(a-1,b),LCS(a,b-1)取最长的情况) 

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/算法基础/Dynamic-Programming_LCS.jpg" alt="LCS" title="LCS" height="200"/>

```
//Dynamic programming
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

#define log(msg) std::cout<<msg<<std::endl

typedef std::vector< std::vector<int> > LCSMap;

void printLCS(const LCSMap & lcs_map){
  for(LCSMap::const_iterator lcs_it=lcs_map.begin();lcs_it!=lcs_map.end();++lcs_it){
    for(std::vector<int>::const_iterator it=lcs_it->begin();it!=lcs_it->end();++it){
      std::cout<<*it<<" ";
    }
    std::cout<<std::endl;
  }
}

int LCS(std::string & a, std::string & b){
  std::vector<int> a_array(a.size(),0);
  LCSMap lcs_map(b.size(),a_array);

  log("LCS input strings: ["<<a<<"],["<<b<<"]");
  log("LCS map before:");
  printLCS(lcs_map);

  int a_size = a.size();
  int b_size = b.size();

  //lcs_map[b][a]
  for(int a_i(0); a_i<a_size; ++a_i){
    for(int b_i(0); b_i<b_size; ++b_i){
      if(a[a_i]==b[b_i]){
        //get left&top +1
        if(a_i-1<0 || b_i-1<0){continue;}
        lcs_map[b_i][a_i] = lcs_map[b_i-1][a_i-1]+1;
      }else{
        //get max(left,top)
        int left(0),top(0);
        if(a_i-1>0) left = lcs_map[b_i][a_i-1];
        if(b_i-1>0) top  = lcs_map[b_i-1][a_i];
        lcs_map[b_i][a_i]= std::max(left,top);
      }
    }
  }

  log("LCS map after:");
  printLCS(lcs_map);

  return lcs_map.back().back();
}


int main(){
  log("LCS:");

  //xyz
  std::string a="abxcaybzcabc";
  std::string b="dxefydefzdef";
  int lcs = LCS(a,b);
  log("Longest common subsequence: ["<<lcs<<"]");
}
```

6.同理, 额外思考: 爬楼梯问题