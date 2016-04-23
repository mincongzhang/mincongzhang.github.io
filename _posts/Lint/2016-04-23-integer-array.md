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