---
layout: post
title: Median Finding Algorithm
category: 算法
description: Median Finding Algorithm
tags: ["C++","算法"]
---

http://www.cs.cornell.edu/courses/cs2110/2009su/Lectures/examples/MedianFinding.pdf

这个课件看得非常吃力啊. 但实际上貌似就是二分法. 随机选取一个数, 分成比它大的(L)和比它小的(S), 假如均等,这数就是median. 假如不等,递归再查. 
