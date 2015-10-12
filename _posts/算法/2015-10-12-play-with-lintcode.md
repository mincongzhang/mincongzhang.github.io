---
layout: post
title: Play with Lintcode
category: 算法
description: Lintcode
tags: ["C++","算法"]
---

### Number of Islands
http://www.lintcode.com/en/problem/number-of-islands/
Solution: BFS/DFS
```
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
