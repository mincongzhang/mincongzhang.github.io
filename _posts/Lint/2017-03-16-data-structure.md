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
//O(n) solution
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
//O(nlogn) solution


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
