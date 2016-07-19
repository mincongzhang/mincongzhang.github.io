---
layout: post
title: Play with Lintcode - Dynamic Programming
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Maximum Subarray
Given an array of integers, find a contiguous subarray which has the largest sum.  
http://www.lintcode.com/en/problem/maximum-subarray/  

```
//Solution1 O(n^2)
//For every element, go forward and find max, update global max

//Solution2 O(nlogn)
//Binary search, search left, and search right, and recursively solve the problem

//Solution3 O(n) update local max and global max at the same time
class Solution {
public:
  /**
   * @param nums: A list of integers
   * @return: A integer indicate the sum of max subarray
   */
  int maxSubArray(vector<int> nums) {
    if(nums.empty()) return 0;

    int local_max(INT_MIN),max(INT_MIN);
    for(size_t i=0; i<nums.size(); ++i){
      if(local_max<0){
        local_max = nums[i];
      } else {
        local_max += nums[i];
      }

      if(local_max > max){
        max = local_max;
      }
    }

    return max;
  }
};
```

### Climbing Stairs
You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?  
http://www.lintcode.com/en/problem/climbing-stairs/  

```
class Solution {
public:
  /**
   * @param n: An integer
   * @return: An integer
   */
  int climbStairs(int n) {
    if(n<=0) return 0;
    if(n==1) return 1;
    if(n==2) return 2;
    return climbStairs(n-1) + climbStairs(n-2);
  }
};
```

### Triangle
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.  
http://www.lintcode.com/en/problem/triangle/  

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &A) {
        if(A.empty())     return 0;
        if(A.size() == 1) return A[0][0];
        
        const unsigned int row_num = A.size();
        unsigned int row,row_elem;
        
        //end at last-1 row
        for(row = 0; row < row_num-1; ++row){  
            //first elem of row+1
            A[row+1][0] += A[row][0];
            
            //start from 2nd element
            for(row_elem = 1; row_elem < A[row].size(); row_elem++){ 
                if(A[row][row_elem-1] < A[row][row_elem])
                    A[row+1][row_elem] += A[row][row_elem-1];
                else
                    A[row+1][row_elem] += A[row][row_elem];
            }
            
            //last elem of row+1
            A[row+1][A[row].size()] += A[row][A[row].size()-1];
        }
        
        //loop last row, get min
        vector<int> last_row = A[A.size()-1];
        int min = last_row[0];
        for(vector<int>::const_iterator i=last_row.begin();i!=last_row.end();++i){
            if(min > *i) min = *i;
        }
        
        return min;
    }
};
```

### Unique Paths

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
How many possible unique paths are there?

http://www.lintcode.com/en/problem/unique-paths/

```
class Solution {
public:
    /**
     * @param n, m: positive integer (1 <= n ,m <= 100)
     * @return an integer
     */
  int uniquePaths(int m, int n) {
    if(m <= 1 || n <= 1) return 1;

    std::vector<int> path_array(n,1);
    std::vector< std::vector<int> > path_map(m,path_array);

    for(int m_i=1; m_i<m; ++m_i){
      for(int n_i=1; n_i<n; ++n_i){
        path_map[m_i][n_i] = path_map[m_i-1][n_i]+path_map[m_i][n_i-1];
      }
    }

    return path_map.back().back();
  }
};

```

### Unique Binary Search Trees
Given n, how many structurally unique BSTs (binary search trees) that store values 1...n?
http://www.lintcode.com/en/problem/unique-binary-search-trees/

reference:http://fisherlei.blogspot.co.uk/2013/03/leetcode-unique-binary-search-trees.html
```
这题想了好久才想清楚。其实如果把上例的顺序改一下，就可以看出规律了。
 1                1                      2                       3             3
    \                 \                 /      \                  /              / 
      3               2              1       3               2             1
    /                   \                                       /                  \
 2                       3                                   1                    2

比如，以1为根的树有几个，完全取决于有二个元素的子树有几种。同理，2为根的子树取决于一个元素的子树有几个。以3为根的情况，则与1相同。

定义Count[i] 为以[0,i]能产生的Unique Binary Tree的数目，

如果数组为空，毫无疑问，只有一种BST，即空树，
Count[0] =1

如果数组仅有一个元素{1}，只有一种BST，单个节点
Count[1] = 1

如果数组有两个元素{1,2}， 那么有如下两种可能
1                       2
  \                    /
    2                1
Count[2] = Count[0] * Count[1]   (1为根的情况)
                  + Count[1] * Count[0]  (2为根的情况。

再看一遍三个元素的数组，可以发现BST的取值方式如下：
Count[3] = Count[0]*Count[2]  (1为根的情况)
               + Count[1]*Count[1]  (2为根的情况)
               + Count[2]*Count[0]  (3为根的情况)

所以，由此观察，可以得出Count的递推公式为
Count[i] = ∑ Count[0...k] * [ k+1....i]     0<=k<i-1
问题至此划归为一维动态规划。
```

```
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: An integer
     */
    int numTrees(int n) {
        if(n<=1) return 1;
        
        std::vector<int> count(n+1,0);
        count[0] = 1;
        count[1] = 1;
        
        for(int i(2); i<count.size(); ++i){
            for(int j(0); j<i; ++j){
                count[i] += count[j]*count[i-j-1];
            }
        }
        
        return count.back();
    }
};
```
