---
layout: post
title: EDX - Karp-Rabin算法(String Searching)
category: 算法基础
description: 
tags: ["C++","算法"]
---

### Karp-Rabin算法:串即是数
1.凡物皆数:
(1)用数表示pattern,就可以有O(text_len)的复杂度了?
(2)串与串的比对,可以变成数与数的比对,于是比对那部分就是O(1)

2.素数又要来了!

3.每个有限维的自然数向量,唯一对应于一个自然数(就是指纹finger print):
e.g.
设p(k)是第k个素数:2,3,5,7,11...
向量[a1 a2 a3 a4 a5 ...] ~ p(1)^(a1+1) * p(2)^(a2+1)...

e.g.
[3 1 4 1 5 9 2 6] =
2^4 * 3^2 * 5^5 * 7^2 * 11^6...

并且还可以还原
*但是我没明白为什么要(an+1),哦哦是不是怕0的出现?

4.串亦为数
(1)十进制,可直接视作自然数
(2)设d为字符集的size,每个字符串都对应于一个d进制自然数(尽管不是单射)
e.g. 
CAT = 2 0 19 (字母表26进制) = 1371 (10进制)
ABBA = 0 1 1 0 (字母表26进制) = 702 (10进制)

### Karp-Rabin算法:散列(hash)
1.问题:数位溢出
(1)如果字符集很大,模式串pattern较长,对应的指纹(finter print)将会很大
e.g.
以ASCII为例,字符集有2^7 = 128
只要size(Pattern)>9,指纹的长度至少是 7*10 = 70bits
(2)而且目前的字长一般不超过64bit,储存不便
(3)这样指纹的计算和比对,就不能在O(1)时间内完成
(4)需要O(pattern_len/64)=O(pattern_len)的时间,总体需要O(pattern_len*text_len)时间(又和brute force一样了?)

2.所以需要散列压缩
(1)基本构思:压缩指纹,对比压缩后的指纹确定匹配位置
(2)借助散列,将指纹压缩至储存器支持的范围
e.g.模余函数:hash(key) = key % M  9 (M=97)

3.应对散列冲突
(1)hash值相等,并非匹配的充分条件
(2)通过hash筛选,还需经过严格比对,才可以最终确定是否匹配

4.指纹更新
(1)在text里的hash指纹计算,似乎每次都需要O(pattern_len)的时间,有可能加速吗?
(2)回忆一下进制转换算法(我还不会....),利用相邻数位间的相关性:
-相邻两次计算之间,  存在着某种相关性
-相邻的两个指纹之间,也有着某种相关性

```
void updateHash ( HashCode& hashT, char* text, size_t m, size_t k, HashCode Dm ) {
	hashT = ( hashT - text[k-1] * Dm ) % M; //在前一指纹基础上，去除首位T[k - 1]
	hashT = ( hashT * R + text[k+m - 1] % M; //添加末位T[k + m - 1]
	if ( 0 > hashT ) hashT += M; //确保散列码落在合法区间内
}
```