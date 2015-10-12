---
layout: post
title: Lintcode练习(Play with Lintcode)
category: 算法
description: Lintcode
tags: ["C++","算法"]
---

### Number of Islands
//http://www.lintcode.com/en/problem/number-of-islands/

```
//Solution: BFS/DFS

typedef unsigned int uint;

class Solution {
private:
    uint m_row_num;
    uint m_col_num;

    void search(vector<vector<bool>> & grid, int row, int col){
        
        if ( row<0 || col<0 || row>m_row_num || col>m_col_num) {
            return;
        }
        
        if (!grid[row][col]){
            return;
        }

        grid[row][col] = false;
        
        search(grid,row-1,col);
        search(grid,row,col-1);
        search(grid,row+1,col);
        search(grid,row,col+1);
    }
    
public:
    /**
     * @param grid a boolean 2D matrix
     * @return an integer
     */
    int numIslands(vector<vector<bool>> & grid) {
        if(grid.empty()) return 0;
        
        m_row_num = grid.size() - 1;
        m_col_num = grid[0].size() - 1;
        int num_islands = 0;
        
        for (uint r(0); r <= m_row_num; ++r){
            for (uint c(0); c <= m_col_num; ++c){
                if(grid[r][c]){
                    search(grid, r, c);
                    num_islands++;
                }
            }
        }
        
        return num_islands;
    }
};
```

### Number of Islands II
http://www.lintcode.com/en/problem/number-of-islands-ii/#

```
//Solution: BFS/DFS for given points.
//Problem: cannot print if the point is given one by one.

/**
 * Definition for a point.
 * struct Point {
 *     int x;
 *     int y;
 *     Point() : x(0), y(0) {}
 *     Point(int a, int b) : x(a), y(b) {}
 * };
 */
 
 
#include <set>
#include <stdlib.h>
#include <iostream>

class Solution {
private:

vector<Point> * m_p_operators;
int m_island_count;
vector<int> m_island_count_vec;

public:
    /**
     * @param n an integer
     * @param m an integer
     * @param operators an array of point
     * @return an integer array
     */
     
    bool linked(const Point & p1, const Point & p2){
        int x_diff = p1.x - p2.x;
        int y_diff = p1.y - p2.y;
        int diff = std::abs(x_diff)+std::abs(y_diff);
        return diff < 2;
    }
    
    void search(std::vector<Point>::iterator & it){
        if(it == m_p_operators->end()) return;
        
        Point cur_p = *it;
        m_p_operators->erase(it);//after erase, it = next
        m_island_count_vec.push_back(m_island_count);
        
        if(it == m_p_operators->end()) return;
        if(linked(cur_p,*it)){
            search(it);
        }
    }
     
    vector<int> numIslands2(int n, int m, vector<Point>& operators) {
        vector<int> ret;
        if (operators.empty()) return ret;
    
        m_p_operators = & operators;
        m_island_count = 1;
        
        while(!operators.empty()){
            Point p_test = *(operators.begin());
    
            std::vector<Point>::iterator it = operators.begin();
            while(it != m_p_operators->end()){
                if(linked(p_test,*it)) search(it);
                else                   ++it;
            }
            
            ++m_island_count;
        }
        
        return m_island_count_vec;
    }
};
```
