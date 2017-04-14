---
layout: post
title: Play with Lintcode - Data Structure
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Min Stack

Implement a stack with min() function, which will return the smallest number in the stack.
It should support push, pop and min operation all in O(1) cost.
http://www.lintcode.com/en/problem/min-stack/

```
class MinStack {
private:
    int m_min;
    std::stack<int> m_stack;
    std::stack<int> m_min_stack;

public:
    MinStack() {
        m_min = INT_MAX;
    }

    void push(int number) {
        if(number <= m_min){
            m_min_stack.push(number);
            m_min = number;
        }

        m_stack.push(number);
    }

    int pop() {
        if(m_stack.empty()) return INT_MAX;
        
        int ret = m_stack.top();
        //when ret is the min of the stack
        if(!m_min_stack.empty() && ret == m_min_stack.top()){
            m_min_stack.pop();
            //update m_min
            if(!m_min_stack.empty()){
                m_min = m_min_stack.top();
            } else {
                m_min = INT_MAX;
            }
        }
        
        m_stack.pop();
        return ret;
    }

    int min() {
        return m_min;
    }
};
```

### Implement Queue by Two Stacks

The queue should support push(element), pop() and top() where pop is pop the first(a.k.a front) element in the queue.
Both pop and top methods should return the value of first element.

http://www.lintcode.com/en/problem/implement-queue-by-two-stacks/ 

```

namespace {
  const int EMPTY_VAL = 0;
}

class MyQueue {
private:
  stack<int> stack1;
  stack<int> stack2;
  int m_top;
  void stackSwap(stack<int> & stack_a, stack<int> & stack_b){
    while(!stack_a.empty()){
      stack_b.push(stack_a.top());
      stack_a.pop();
    }
  }

public:

  MyQueue() {
    m_top = EMPTY_VAL;
  }

  void push(int element) {
    if(stack1.empty()) {
      m_top = element;
    }

    stack1.push(element);
  }

  int pop() {
    int pop_val = EMPTY_VAL;
    if(stack1.empty()){
      return pop_val;
    }
    stackSwap(stack1,stack2);

    pop_val = m_top;
    stack2.pop();
    //update top after pop
    if(!stack2.empty()){
      m_top = stack2.top();
    } else {
      m_top = EMPTY_VAL;
    }

    stackSwap(stack2,stack1);

    return pop_val;
  }

  int top() {
    return m_top;
  }
};
```

### Longest Consecutive Sequence
Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.

```
//O(nlogn) solution (std::set)
#include <algorithm>

class Solution {
public:
  /**
   * @param nums: A list of integers
   * @return an integer
   */
  int longestConsecutive(vector<int> &num) {
    if(num.empty()) return 0;

    std::set<int> sorted_num;
    for(std::vector<int>::const_iterator it=num.begin(); it!=num.end(); ++it){
      sorted_num.insert(*it);
    }

    std::set<int>::const_iterator it=sorted_num.begin();
    int prev = *it;
    it++;

    int count = 1;
    int max_count = 1;
    while(it!=sorted_num.end()){
      if(abs(*it - prev)==1){
        count++;
        max_count = std::max(count,max_count);
      } else {
        count = 1;
      }
      
      prev=*it;
      ++it;
    }

    return max_count;
  }

};
```

```
//O(nlogn) solution (sort)

#include <algorithm>

class Solution {
public:
  /**
   * @param nums: A list of integers
   * @return an integer
   */
  int longestConsecutive(vector<int> &num) {
    if(num.empty()) return 0;

    std::sort(num.begin(),num.end());
    int count = 1;
    int max_count = 1;
    for(size_t i=1; i< num.size(); ++i){
      int diff = abs(num[i] - num[i-1]);

      //1. diff == 1, Consecutive
      if(diff==1){
        count++;
        max_count = std::max(count,max_count);
        continue;
      }

      //2. diff > 1, not Consecutive
      //reset if diff >1
      if(diff>1){
        count = 1;
      }

      //3. diff == 0, no count
      //when diff == 0, continue
    }

    return max_count;
  }
};
```

```
//O(n) solution: hashtable

#include <unordered_map>

class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return an integer
     */
    int longestConsecutive(vector<int> &num) {
        std::unordered_set<int> hash;
        for(size_t i=0;i<num.size();++i){
            hash.insert(num[i]);
        }
        
        int length = INT_MIN;
        while(!hash.empty()){
            int cur = *hash.begin();
            int count = 1;
            
            hash.erase(cur);
            int next = cur+1;
            
            std::unordered_set<int>::const_iterator it=hash.find(next);
            while(it!=hash.end()){
                hash.erase(next);
                count++;
                next++;
                it=hash.find(next);
            }
            
            int prev = cur-1;
            it = hash.find(prev);
            while(it!=hash.end()){
                hash.erase(prev);
                count++;
                prev--;
                it=hash.find(prev);
            }
            
            length = std::max(count,length);
        }
        
        return length;
    }
};
```

### Data Stream Median

Numbers keep coming, return the median of numbers at every time a new number added.

http://www.lintcode.com/en/problem/data-stream-median/

```
#include <functional>   // std::greater
#include <queue>
class Solution {
public:
  /**
   * @param nums: A list of integers.
   * @return: The median of numbers
   */
  vector<int> medianII(vector<int> &nums) {
    std::priority_queue<int,std::vector<int>, std::greater<int> > larger_half;   //pop: 5 6 7 8 9
    std::priority_queue<int,std::vector<int>, std::less<int> >    less_half;     //pop: 4 3 2 1 0

    vector<int> medians;
    for(vector<int>::const_iterator it=nums.begin(); it!=nums.end(); ++it){

      const int & num = *it;

      //push to queue
      if(less_half.empty() || num < less_half.top()){
        less_half.push(num);
      } else {
        larger_half.push(num);
      }

      //balance
      if(less_half.size() > larger_half.size()+1){
        larger_half.push(less_half.top());
        less_half.pop();
      }
      if(larger_half.size() > less_half.size()){
        less_half.push(larger_half.top());
        larger_half.pop();
      }


      //push to medians
      medians.push_back(less_half.top());
    }

    return medians;
  }
};

```

### Largest Rectangle in Histogram

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

http://www.lintcode.com/en/problem/largest-rectangle-in-histogram/

```
//brute force

#include <queue>
class Solution {
public:
  /**
   * @param height: A list of integer
   * @return: The area of largest rectangle in the histogram
   */
  int largestRectangleArea(vector<int> &height) {
	int max_area = 0;
  
    int size = height.size();
    for(int i=0; i<size; ++i){
		//backward
		int area = 0;
		int temp_i = i-1;
		while(temp_i>=0){
			if(height[temp_i]>=height[i]){
				area+=height[i];
			} else {
				break;
			}
			temp_i--;
		}

		//forward(self included)
		temp_i = i;
		while(temp_i<size){
			if(height[temp_i]>=height[i]){
				area+=height[i];
			} else {
				break;
			}			
			temp_i++;
		}

		max_area = std::max(area,max_area);
    }
    
    return max_area;
  }
};
```

### Merge k Sorted Lists

Merge k sorted linked lists and return it as one sorted list.Analyze and describe its complexity.

http://www.lintcode.com/en/problem/merge-k-sorted-lists/

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
 #include <queue>
 
 namespace{
    typedef ListNode* ListNodePtr;
    
    struct Compare {
        bool operator()(const ListNodePtr & l1, const ListNodePtr & l2){
            return l1->val > l2->val;
        }
    };
 }
 
class Solution {

private:
    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        ListNodePtr pre_head = new ListNode(0);
        ListNodePtr head = pre_head;
        while(l1 != NULL && l2 != NULL){
            if(l1->val <= l2->val){
                head->next = l1;
                head = head->next;
                l1 = l1->next;
                continue;
            }
            
            head->next = l2;
            head = head->next;
            l2 = l2->next;
        }
        
        if(l1){
            head->next = l1;
        } else {
            head->next = l2;
        }
        
        return pre_head->next;
    }
    
public:
    /**
     * @param lists: a list of ListNode
     * @return: The head of one sorted list.
     */
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if(lists.empty()) return NULL;
        
        std::priority_queue<int,vector<ListNodePtr>, Compare> q;
        
        for(size_t i=0; i<lists.size();++i){
            if(lists[i] != NULL){
                q.push(lists[i]);
            }
        }
        
        ListNodePtr pre_head = new ListNode(0);
        ListNodePtr head = pre_head;
        while(!q.empty()){
            ListNodePtr cur = q.top();
            q.pop();
            head->next = cur;
            head = head->next;
            if(cur->next != NULL){
                cur = cur->next;
                q.push(cur);
            }
        }
        
        return pre_head->next;
    }
};
```
