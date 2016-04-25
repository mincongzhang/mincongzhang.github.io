### 2 Sum
Given an array of integers, find two numbers such that they add up to a specific target number.
http://www.lintcode.com/en/problem/two-sum/

```
//Hash Map Solution

#include <algorithm>
#include <unordered_map>
#include <utility>//pair

class Solution {
private:
    void sortBeginEnd(std::vector<int> & begin_end){
        if(begin_end.size()!=2) return;
        if(begin_end[0] < begin_end[1]) return;
        std::swap(begin_end[0],begin_end[1]);
    }

public:
    /*
     * @param numbers : An array of Integer
     * @param target : target = numbers[index1] + numbers[index2]
     * @return : [index1+1, index2+1] (index1 < index2)
     */
    typedef std::unordered_map<int,int> DiffMap;
    
    vector<int> twoSum(vector<int> &nums, int target) {
        vector<int> begin_end(2,0);
        
        if(nums.empty()){ return begin_end; }
        
        // NOTE: 无序数组的题目如果要O(n)解法往往要用到hash table
        DiffMap diff_map;
        
        for(int it(0); it<nums.size(); ++it){
            diff_map.insert(std::pair<int,int>(nums[it],it));
        }
 
        DiffMap::const_iterator map_it; 
        for(int it(0); it<nums.size(); ++it){
            map_it = diff_map.find(target-nums[it]);
            if(map_it == diff_map.end()){ 
                continue;
            }
            
            //In case of [0,4,3,0], 0
            if(map_it->second == it){
                continue;
            }
            
            begin_end[0] = it+1;
            begin_end[1] = (map_it->second) + 1;
            sortBeginEnd(begin_end);
            return begin_end;
        }
        
        return begin_end;
    }
};
```

```
//Sorting Solution
#include <algorithm>
#include <map>
#include <utility>//pair
#include <iostream>


class Solution {
private:
    void sortBeginEnd(std::vector<int> & begin_end){
        if(begin_end.size()!=2) return;
        if(begin_end[0] < begin_end[1]) return;
        std::swap(begin_end[0],begin_end[1]);
    }
    
public:
    /*
     * @param numbers : An array of Integer
     * @param target : target = numbers[index1] + numbers[index2]
     * @return : [index1+1, index2+1] (index1 < index2)
     */
    typedef std::multimap<int,int> IndexMap; //multimap: //in case [0,4,3,0], 0
    
    vector<int> twoSum(vector<int> &nums, int target) {
        vector<int> begin_end(2,0);
        
        if(nums.empty()){ return begin_end; }
        
        IndexMap index_map; 
        for(int it(0); it < nums.size(); ++it){
            index_map.insert(std::pair<int,int>(nums[it],it));
        }
        
        IndexMap::const_iterator map_begin = index_map.begin();
        IndexMap::reverse_iterator map_end = index_map.rbegin();
        while(map_begin->second != map_end->second){
            //std::cout<<map_begin->first<<" "<<map_end->first<<std::endl;
            //std::cout<<map_begin->second<<" "<<map_end->second<<std::endl;
            if(map_begin->first + map_end->first > target){
                //std::cout<<">"<<std::endl;
                map_end++;
                continue;
            }
            
            if(map_begin->first + map_end->first < target){
                //std::cout<<"<"<<std::endl;
                map_begin++;
                continue;
            }

            begin_end[0] = map_begin->second+1;
            begin_end[1] = map_end->second+1;
            sortBeginEnd(begin_end);
            return begin_end;
        }
        
        return begin_end;
    }
};
```