---
layout: post
title: Play with Lintcode - Integer Array
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Remove Element
Given an array and a value, remove all occurrences of that value in place and return the new length.
http://www.lintcode.com/en/problem/remove-element/

```
#include <iostream>
#include <algorithm>

class Solution {
public:
    /** 
     *@param A: A list of integers
     *@param elem: An integer
     *@return: The new length after remove
     */
    int removeElement(vector<int> &A, int elem) {
        if(A.empty()){ return A.size(); }
        
        int size = A.size();
        int delete_it(0),it(0);
        
        for(; it<size; ++it){
            if(A[it] == elem){
                if(A[delete_it] != elem){
                    delete_it = it;
                }
            } else {
                std::swap(A[delete_it],A[it]);
                delete_it++;
            }
        }
        
        return delete_it;
    }
};
```

#Subarray Sum
Given an integer array, find a subarray where the sum of numbers is zero. 
http://www.lintcode.com/en/problem/subarray-sum/

```
#include <unordered_map>
#include <utility> //pair

class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number 
     *          and the index of the last number
     */
     
     typedef std::unordered_map<int,std::vector<int>> CumSumMap;
    vector<int> subarraySum(vector<int> nums){
        std::vector<int> ret_begin_end(2,0);
        if(nums.empty()) return ret_begin_end;
        
        CumSumMap cum_sum_map;
        cum_sum_map.insert(std::pair< int, std::vector<int> >(0,ret_begin_end));
        
        int count(0);
        for(int it(0); it < nums.size(); ++it){
            count += nums[it];
            
            CumSumMap::const_iterator map_it = cum_sum_map.find(count);
            std::vector<int> begin_end(2,0);
            if(map_it == cum_sum_map.end()){
                begin_end[0] = it+1;
                cum_sum_map.insert(std::pair< int, std::vector<int> >(count,begin_end));
            } else {
                begin_end =(map_it->second);
                begin_end[1] = it;
                return begin_end;
            }
        }
        
        return ret_begin_end;
    }
};
```

### Product of Array Exclude Itself
Given an integers array A. Define B[i] = A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1], calculate B WITHOUT divide operation.
http://www.lintcode.com/problem/product-of-array-exclude-itself

```
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        int size = nums.size();
        std::vector<long long> result(size,0);
        if(nums.empty()) return result;
        
        //Get products of left and right 
        std::vector<long long> left(size,1), right(size,1);
        for(int it(1); it < size; ++it){
            left[it] = left[it-1] * nums[it-1];
            
            int right_it = size-it-1; 
            right[right_it] = right[right_it+1] * nums[right_it+1];
        }
        
        //get result
        for(int it(0); it < size; ++it){
            result[it] = left[it] * right[it];
        }
        
        return result;
    }
};
```

```
//In Place Solution
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        int size = nums.size();
        std::vector<long long> result(size,1);
        if(nums.size()<=1) return result;
        
        //Get products of right
        for(int it(size-1-1); it >=0 ; --it){
            result[it] *= result[it+1] * nums[it+1];
        }
        
        //get result
        long long temp(1);
        for(int it(0); it < size; ++it){
            result[it] *= temp;
            temp *= nums[it];
        }
        
        return result;
    }
};
```

### Merge Sorted Array
Given two sorted integer arrays A and B, merge B into A as one sorted array.
http://www.lintcode.com/en/problem/merge-sorted-array/

```
#include <algorithm>
#include <iostream>

class Solution {
public:
    /**
     * @param A: sorted integer array A which has m elements, 
     *           but size of A is m+n
     * @param B: sorted integer array B which has n elements
     * @return: void
     */
    void mergeSortedArray(int A[], int m, int B[], int n) {

        int A_it = m - 1;
        int B_it = n - 1;
        int A_back = m + n; //dont know, what if there are same element in A and B?
        
        while(A_back > 0){
            A_back--;
            if(B_it < 0 || A[A_it] >= B[B_it]){  //NOTE: have to be ">=" 
                A[A_back] = A[A_it];
                A_it--;
                continue;
            } 
            
            if(A_it < 0 || B[B_it] >= A[A_it]){
                A[A_back] = B[B_it];
                B_it--;
                continue;
            }
        }

    }
};
```