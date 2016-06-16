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


### 3 Sum
Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.
*Elements in a triplet (a,b,c) must be in non-descending order. (ie, a ≤ b ≤ c)
*The solution set must not contain duplicate triplets.
http://www.lintcode.com/en/problem/3sum/


```
#include <algorithm>
#include <iostream>

class Solution {
private:
vector<vector<int> > m_triplets;

void findTwoSum(const vector<int> & nums,int begin, int end){
    if(begin == end){ return; }
    
    //find 2 sum = -first;
    int sum = -nums[begin];
    
    //NOTE: avoid duplication
    if(!m_triplets.empty() && nums[begin] == m_triplets.back()[0]){
        return;
    }
    
    begin++; //NOTE: avoid duplicated. e.g. nums[begin] = nums[second] = 0.
    
    
    while(begin < end){ 
        if(nums[begin]+nums[end] > sum){
            end--;
            continue;
        }
        
        if(nums[begin]+nums[end] < sum){
            begin++;
            continue;
        }
        
        //NOTE: avoid duplication
        if(!m_triplets.empty() && -sum == m_triplets.back()[0] && 
        nums[begin] == m_triplets.back()[1] && nums[end] == m_triplets.back()[2]){
            end--; //NOTE: may have multiple triplet
            continue;
        }
        
        //NOTE: 3 nums have already been non-descending
        vector<int> triplet;
        triplet.push_back(-sum);
        triplet.push_back(nums[begin]);
        triplet.push_back(nums[end]);
        
        m_triplets.push_back(triplet);
        //break;  NOTE: may have multiple triplet
        end--;
    }
}

public:    
    /**
     * @param numbers : Give an array numbers of n integer
     * @return : Find all unique triplets in the array which gives the sum of zero.
     */
    vector<vector<int> > threeSum(vector<int> &nums) {
        if(nums.empty()) { return m_triplets; }
        
        std::sort(nums.begin(),nums.end());
        
        for(int begin(0), end(nums.size()-1); begin < nums.size(); ++begin){
            findTwoSum(nums,begin,end);
        }
        
        return m_triplets;
    }
};
```


### 3Sum Closest
Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. Return the sum of the three integers.
http://www.lintcode.com/en/problem/3sum-closest/

```
#include <algorithm>
#include <limits.h>
#include <vector>

class Solution {
private:
	int m_closest_sum;
	int m_target;

	void findCloest(const std::vector<int> & nums, int begin, int end){
		int first = nums[begin];
		begin++;

        if(begin == end) return;

		int closest_sum(INT_MAX);
		int min_diff(INT_MAX);
		while(begin < end){
			int cur_diff = std::abs(m_target - (first + nums[begin] + nums[end]));
			if(min_diff < cur_diff){
				break;
			} else {
				min_diff = cur_diff;
				closest_sum = first + nums[begin] + nums[end];
			}

			int left_diff = std::abs(m_target - (first + nums[begin+1] + nums[end]));
			int right_diff = std::abs(m_target - (first + nums[begin] + nums[end-1]));

			if(left_diff < right_diff){
				begin++;
			}	else {
				end--;
			}
		}

		if(std::abs(closest_sum - m_target)<std::abs(m_closest_sum - m_target)){
			m_closest_sum = closest_sum;
		}
	}

public:
	/**
	* @param numbers: Give an array numbers of n integer
	* @param target: An integer
	* @return: return the sum of the three integers, the sum closest target.
	*/
	int threeSumClosest(std::vector<int> nums, int target) {
		m_closest_sum = INT_MAX;
		if(nums.empty()) return m_closest_sum;

		m_target = target;
		std::sort(nums.begin(),nums.end());

		for(int begin(0),end(nums.size()-1); begin<nums.size(); ++begin){
			//if abs(diff) decrease, keep going
			//else break
			findCloest(nums,begin,end);
		}

		return 	m_closest_sum;
	}
};
```
