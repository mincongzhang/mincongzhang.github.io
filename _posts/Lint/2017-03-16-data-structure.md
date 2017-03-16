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
