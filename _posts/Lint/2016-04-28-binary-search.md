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
            if(mid_val > m_target){
                end = mid-1;//NOTE:beware
                continue;
            }
        
            if(mid_val < m_target){
                begin = mid+1;//NOTE: this happens again...NOTE: because int mid = floor((begin+end)/2)
                continue;
            }
            
            return true;
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