---
layout: post
title: 最大子序列求和问题(联机算法 on-line algorithm)
category: 算法
description: 最大子序列求和问题
tags: ["C++","算法"]
---

联机算法：在任意时刻，算法对要操作的数据只读入（扫描）一次，一旦被读入并处理，它就不需要在被记忆了。而在此处理过程中算法能对它已经读入的数据立即给出相应子序列问题的正确答案。具有这种特性的算法叫做联机算法（on-line algorithm）。

```
int maxSubSum4(const vector<int> & a)
{
	int maxSum = 0;
	int nowSum = 0;

	for (int j = 0; j < a.size(); j++)
	{
		nowSum += a[j];

		if(nowSum > maxSum)
			maxSum = nowSum;
		else if(nowSum < 0)
			nowSum = 0;
	}

	return maxSum;
}
```

reference: http://blog.csdn.net/pleasecallmewhy/article/details/28641625