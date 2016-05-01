---
layout: post
title: Play with Lintcode - Binary Search
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Search Insert Position
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

http://www.lintcode.com/en/problem/search-insert-position/

```
class Solution {
  /**
   * param A : an integer sorted array
   * param target :  an integer to be inserted
   * return : an integer
   */
public:
  int searchInsert(vector<int> &A, int target) {
    if(A.empty()){ return 0; }

    int begin(0),end(A.size()-1);
    while(begin<end){
      int mid = (begin+end)/2;
      if(A[mid] > target){
        end = mid;
        continue;
      }

      if(A[mid] < target){
        begin = mid+1; //NOTE: because int mid = floor((begin+end)/2)
        continue;
      }

      return mid;
    }

    if(begin == 0 && A[begin]>target) return begin;
    if(begin == A.size()-1 && A[begin]<target) return begin+1;
    return begin;
  }
};

```

### Sqrt
Implement int sqrt(int x).
http://www.lintcode.com/en/problem/sqrtx/

```
//Newton's method

#include <iostream>
class Solution {
public:
    /**
     * @param x: An integer
     * @return: The sqrt of x
     */
    int sqrt(int x) {
        if(x == 0) return 0;
        
        //sqrt(x)
        //https://en.wikipedia.org/wiki/Methods_of_computing_square_roots
        
        double s = double(x)/2.0;
        while(true){
            double s_n = 0.5*(s + x/s);
            if(int(s_n) == int(s)){ break; }
            
            s = s_n;
        }
        
        return int(s);
    }
};
```

```
//binary search 
#include <iostream>
class Solution {
public:
    /**
     * @param x: An integer
     * @return: The sqrt of x
     */
    int sqrt(int x) {
        if(x == 1) return x;

        long begin(0),end(x);
        while(begin+1 < end){
            long mid = (begin + end)/2;
            long square = mid*mid;
            
            if(square > x){
                end = mid;
                continue;
            }
            
            if(square < x){
                begin = mid;
                continue;
            }
            
            return mid;
        }
    
        return begin;
    }
};
```