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

###
sqrt:
http://bangbingsyb.blogspot.co.uk/2014/11/leetcode-sqrtx.html
https://en.wikipedia.org/wiki/Methods_of_computing_square_roots
http://www.matrix67.com/blog/archives/361