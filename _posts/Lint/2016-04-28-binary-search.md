---
layout: post
title: Play with Lintcode - Binary Search
category: Lint
description: Lintcode
tags: ["C++","算法"]
---


### First Position of Target
For a given sorted array (ascending order) and a target number, find the first index of this number in O(log n) time complexity.
If the target number does not exist in the array, return -1.
http://www.lintcode.com/en/problem/first-position-of-target/

```
class Solution {
private:
  int m_pos;
  void search(const vector<int> & array,const int & target, int start, int end){
    if(start > end) return;
    int half = (start+end)/2;

    if(start == end ){
      if(array[half] == target)
        m_pos = half;
      
      return;
    }

    if(array[half] >= target){
      search(array,target,0,half);
    } else {
      search(array,target,half+1,end);
    }
  }

public:
  typedef unsigned int uint;
  int binarySearch(vector<int> &array, int target) {
    m_pos = -1;
    search(array,target,0,array.size()-1);

    return m_pos;
  }
};

```

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

### Search for a Range
Given a sorted array of n integers, find the starting and ending position of a given target value.
If the target is not found in the array, return [-1, -1].

http://www.lintcode.com/en/problem/search-for-a-range/

```
class Solution {
private:
    void getRange(const vector<int> & A, int target, int target_i, vector<int> & begin_end){
        int begin(target_i),end(target_i);
        while(begin >= 0){
            if(A[begin] != target){ break; }
            begin_end[0] = begin;
            begin--;
        }
        
        while(end < A.size()){
            if(A[end] != target) { break; }
            begin_end[1] = end;
            end++;
        }
    }
    
    /** 
     *@param A : an integer sorted array
     *@param target :  an integer to be inserted
     *return : a list of length 2, [index1, index2]
     */
public:
    vector<int> searchRange(vector<int> &A, int target) {
        vector<int> begin_end(2,-1);
        if(A.empty()) return begin_end;
        
        int target_i(-1);
        int begin(0),end(A.size()-1);
        while(begin <= end){
            int mid = (begin + end)/2;
            
            if(A[mid] > target){
                end = mid-1;
                continue;
            }
            
            if(A[mid] < target){
                begin = mid+1;
                continue;
            }
            
            target_i = mid;
            break;
        }
        
        if(target_i == -1) { return begin_end; }
        getRange(A,target,target_i,begin_end);
        return begin_end;
    }
};
```


### Search a 2D Matrix
Write an efficient algorithm that searches for a value in an m x n matrix.
This matrix has the following properties:
*Integers in each row are sorted from left to right.
*The first integer of each row is greater than the last integer of the previous row.

```
typedef vector<vector<int> > Matrix;

class Solution {
private:
    int m_target;

    int searchColumn(const vector<vector<int> > & matrix){
        int begin = 0;
        int end   = matrix.size()-1;
    
        while(begin+1 < end){
            int mid = (begin+end)/2;
            int mid_val = matrix[mid][0];
            if(mid_val > m_target){
                end = mid;
                continue;
            }
        
            if(mid_val < m_target){
                begin = mid+1;
                continue;
            }
            
            return mid;
        }
        
        return begin;
    }
    
    
    bool searchRow(const vector<int> & row){
        int begin = 0;
        int end = row.size()-1;
        while(begin <= end){
            int mid = (begin+end)/2;
            int mid_val = row[mid];
            
            if(mid_val == m_target) return true;
            
            if(mid_val > m_target){
                end = mid-1;//NOTE:beware
            } else {
                begin = mid+1;//NOTE: this happens again...NOTE: because int mid = floor((begin+end)/2)
            }
        }
        
        return false;
    }
    
public:
    /**
     * @param matrix, a list of lists of integers
     * @param target, an integer
     * @return a boolean, indicate whether matrix contains target
     */
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        if(matrix.empty()) return false;
        m_target = target;
        
        int col = searchColumn(matrix);
        if(matrix[col][0] == m_target){
            return true;
        }

        return searchRow(matrix[col]);
    }
};

```

### Find Minimum in Rotated Sorted Array
Suppose a sorted array is rotated at some pivot unknown to you beforehand.
(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array/

```
class Solution {
public:
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    int findMin(vector<int> &num) {
        if(num.empty()) return 0;
        
        int begin(0),end(num.size()-1);
        while(begin+1<end){
            int mid = (begin+end)/2;
            
            if(num[begin] > num[mid]){
                end = mid;
                continue;
            }
        
            if(num[mid] > num[end]){
                begin = mid;
                continue;
            }
            
            return num[begin]<num[end]? num[begin] : num[end];
        }
        
        return num[begin]<num[end]? num[begin] : num[end];
    }
};
```

###　Search in Rotated Sorted Array
Suppose a sorted array is rotated at some pivot unknown to you beforehand.
(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
You are given a target value to search. If found in the array return its index, otherwise return -1.
You may assume no duplicate exists in the array.
http://www.lintcode.com/en/problem/search-in-rotated-sorted-array/

```
class Solution {
    /** 
     * param A : an integer ratated sorted array
     * param target :  an integer to be searched
     * return : an integer
     */
public:
    int search(vector<int> &A, int target) {
        if(A.empty()) return -1;
        
        int begin(0),end(A.size()-1);
        
        while(begin <= end){
            int mid = (begin+end)/2;
            if(A[mid] == target) return mid;
            
            //[begin,mid] unorder
            if(A[mid] < A[end]){
                if(target > A[mid] && target <=A[end]){ //NOTE: beware of the "<="
                    begin = mid + 1;
                } else {
                    end = mid-1;
                }
            //[mid,end] unorder
            } else {
                if(target >= A[begin] && target < A[mid]){  //NOTE: beware of the ">="
                    end = mid-1;
                } else {
                    begin = mid+1;
                }
            }
        }
        
        return -1;
    }
};
```

### First Bad Version
he code base version is an integer start from 1 to n. One day, someone committed a bad version in the code case, so it caused this version and the following versions are all failed in the unit tests. Find the first bad version.

You can call isBadVersion to help you determine which version is the first bad one. The details interface can be found in the code's annotation part.
http://www.lintcode.com/en/problem/first-bad-version/

```
/**
 * class SVNRepo {
 *     public:
 *     static bool isBadVersion(int k);
 * }
 * you can use SVNRepo::isBadVersion(k) to judge whether 
 * the kth code version is bad or not.
*/
class Solution {
public:
    /**
     * @param n: An integers.
     * @return: An integer which is the first bad version.
     */
    int findFirstBadVersion(int n) {
        if(n == 0) return 0;
        
        int begin(0),end(n);
        //find first "true"
        while(begin+1 < end){
            int mid = (begin+end)/2;
            
            if(SVNRepo::isBadVersion(mid)){
                end = mid;
                continue;
            }
            
            //if mid is not bad
            begin = mid;
        }
        
        return end;
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
            
            if(square == x) return mid;
            if(square > x){
                end = mid;
                continue;
            } else {
                begin = mid;
                continue;
            }
        }
    
        return begin;
    }
};
```

### Wood Cut
Given n pieces of wood with length L[i] (integer array). Cut them into small pieces to guarantee you could have equal or more than k pieces with the same length. What is the longest length you can get from the n pieces of wood? Given L & k, return the maximum length of the small pieces.

e.g.For L=[232, 124, 456], k=7, return 114.

```
class Solution {
public:
    /** 
     *@param L: Given n pieces of wood with length L[i]
     *@param k: An integer
     *return: The maximum length of the small pieces.
     */
    int woodCut(vector<int> L, int k) {
        if(L.empty()) return 0;
        
        //find max log
        //binary search [0,max_log]
        
        int max_log(0);
        for(int i(0); i<L.size();++i){
            if(L[i]> max_log){ 
                max_log = L[i];
            }
        }
        
        long min_length(0),max_length(max_log);
        while(min_length+1<max_length){
            long cur_length = (min_length+max_length)/2;
            
            int log_count(0);
            for(int i(0); i<L.size();++i){
                log_count += L[i]/cur_length;
            }
            if(log_count < k){
                max_length = cur_length;
                continue;
            }
            
            min_length = cur_length;
        }
        
        return min_length;
    }
};
```