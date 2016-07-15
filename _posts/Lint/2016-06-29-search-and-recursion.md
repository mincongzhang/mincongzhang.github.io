---
layout: post
title: Play with Lintcode - Search and Recursion
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Word Ladder

Given two words (start and end), and a dictionary, find the length of shortest transformation sequence from start to end, such that:
Only one letter can be changed at a time  
Each intermediate word must exist in the dictionary

Given:
start = "hit"
end = "cog"
dict = ["hot","dot","dog","lot","log"]
As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.

http://www.lintcode.com/en/problem/word-ladder/

```
#include <queue>

class Solution {
public:
  /**
   * @param start, a string
   * @param end, a string
   * @param dict, a set of string
   * @return an integer
   */
  int ladderLength(string start, string end, unordered_set<string> &dict) {
    if(start==end) return 1;
    if(start.size()!=end.size()) return 0;

    std::queue<std::string> Q;
    Q.push(start);
    dict.erase(start);

    int length = 2;//start & end

    while(!Q.empty()){
      size_t q_size = Q.size();
      for(size_t q_it=0; q_it<q_size; ++q_it){

        //Search in the dict
        std::string origin_word = Q.front(); Q.pop();
        for(char c='a'; c<='z'; ++c){
          for(size_t word_it=0; word_it<origin_word.size(); ++word_it){
            std::string word = origin_word;
            //if( word[word_it] == c) continue;
            word[word_it] = c;
            if(word==end) return length;
            if(dict.find(word)!=dict.end()){
              Q.push(word);
              dict.erase(word);//NOTE: remember to erase
            }
          }
        }
        //Search done
      }
      length++;
    }

    return 0;
  }
};
```

### Word Ladder II
Given two words (start and end), and a dictionary, find all shortest transformation sequence(s) from start to end, such that:  
Only one letter can be changed at a time  
Each intermediate word must exist in the dictionary  

http://www.lintcode.com/en/problem/word-ladder-ii/

```
#include <vector>
#include <string>
#include <algorithm>

struct WordNode;

struct WordNode{
  std::string word;
  int parent;
  WordNode(const std::string & w,int p=-1):word(w),parent(p){};
};

class WordList{
private:
  WordNode m_root;
  std::string m_end;
  std::unordered_set<std::string> m_dict;
  std::vector<WordNode> m_word_queue;
  std::vector< std::vector<std::string> > m_word_lists;
  bool traceBack(WordNode & node){
    std::vector<std::string> list;
    while(true){
      list.push_back(node.word);
      if(node.parent==-1) break;
      node = m_word_queue[node.parent];
    }

    std::reverse(list.begin(), list.end());
    m_word_lists.push_back(list);
  }

  void levelOrderSearch(){
    //Level order search: find all shortest transformation sequence(s) from start to end
    m_word_queue.push_back(m_root);
    size_t q_it = 0;
    bool found = false;

    while(q_it < m_word_queue.size()){

      //for each level
      size_t cur_size = m_word_queue.size();
      while(q_it < cur_size){
        std::string origin_word = m_word_queue[q_it].word;
        
        for(char c='a'; c<='z'; ++c){
          for(size_t w=0; w<origin_word.size(); ++w){
            std::string word = origin_word;
            word[w] = c;
            //NOTE: a very tricky problem here
            //You cant use the address of m_word_queue
            //Because the vector will reallocate the memory when size grows
            //WordNode node(word,&m_word_queue[q_it]);
            WordNode node(word,q_it);
            if(word == m_end){
              traceBack(node);
              found = true;
            } else if(m_dict.find(word)!=m_dict.end()){
              m_word_queue.push_back(node); 

              m_dict.erase(word);//NOTE: remember to erase
            }
          }
        }
        q_it++;
      }//while(cur_size - q_it > 0)

      if(found) return;
    }

  }

public:
  WordList(const std::string & start,const std::string & end,const std::unordered_set<std::string> & dict):
    m_root(start,-1),m_end(end),m_dict(dict){
    m_dict.erase(start);
  }

  std::vector< std::vector<std::string> > search(){
    levelOrderSearch();
    return m_word_lists;
  }
};

class Solution {
public:
  /**
   * @param start, a string
   * @param end, a string
   * @param dict, a set of string
   * @return a list of lists of string
   */
  vector<vector<string>> findLadders(string start, string end, unordered_set<string> &dict) {
    //Handle unequal size
    std::vector< std::vector<std::string> > results;
    if(start.size() != end.size()) return results;

    //Handle start==end
    std::vector<std::string> word_list(1,start);
    if(start == end){
      results.push_back(word_list);
      return results;
    }

    WordList list(start,end,dict);
    return list.search();
  }
};
```


###  N-Queens
http://www.lintcode.com/en/problem/n-queens/

```
class Solution {
private:
  vector<vector<string> > m_results;

  void record(const vector<vector<bool> > & board, const int n){
    //std::cout<<"Solution:"<<std::endl;

    std::vector<std::string> result;
    for(int r=0; r<n; ++r){
      std::string str_row;
      for(int c=0; c<n; ++c){
        if(board[r][c]) str_row+="Q";
        else  str_row+=".";
      }
      //std::cout<<str_row<<std::endl;
      result.push_back(str_row);
    }

    m_results.push_back(result);
  }

  bool canPlace(const vector<vector<bool> > & board,const int n, const int row, const int col){
    for(int c=col; c>=0; --c){
      if(board[row][c]) return false;
    }

    int r = row;
    int c = col;
    while(r>=0 && c>=0){
      if(board[r][c]) return false;
      r--;
      c--;
    }

    r = row;
    c = col;
    while(r<n && c>=0){
      if(board[r][c]) return false;
      r++;
      c--;
    }

    return true;
  }

  bool solve(vector<vector<bool> > & board, const int n, const int c){
    //go through column "c"
    if(c == n){
      record(board,n);
      return true;
    }

    for(int r=0; r<n; ++r){
      if(canPlace(board,n,r,c)){
        board[r][c] = true;
        solve(board,n,c+1);
        board[r][c] = false;
      }
    }

    return false;
  }

public:
  /**
   * Get all distinct N-Queen solutions
   * @param n: The number of queens
   * @return: All distinct solutions
   * For example, A string '...Q' shows a queen on forth position
   */
  vector<vector<string> > solveNQueens(int n) {
    vector<bool> board_col(n,false);
    vector<vector<bool> > board(n,board_col);
    solve(board,n,0);
    return m_results;
  }
};

```


### Valid Sudoku
Determine whether a Sudoku is valid.  
A valid Sudoku board (partially filled) is not necessarily solvable. Only the filled cells need to be validated.  
http://www.lintcode.com/en/problem/valid-sudoku/  

```
class Solution {
private:
  vector<vector<int>> m_board;
  size_t m_size;

  void getBoard(const vector<vector<char>>& board){
    vector<int> array(board.size(),0);
    vector<vector<int>> tmp_board(board.size(),array);
    m_board = tmp_board;

    for(size_t r=0; r<m_size; ++r){
      for(size_t c=0; c<m_size; ++c){
        if(board[r][c] <= '9' && board[r][c] >= '1'){
          m_board[r][c] = int(board[r][c] - '0');
        } else {
          m_board[r][c] = 0;
        }
        //std::cout<<m_board[r][c];
      }
      //std::cout<<std::endl;
    }
  }

  bool canPlace(size_t row,size_t col,int num){
    //Row
    for(size_t r=0; r<m_size; ++r){
      if(r == row) continue;
      if(m_board[r][col] == num) return false;
    }

    //Col
    for(size_t c=0;c<m_size; ++c){
      if(c == col) continue;
      if(m_board[row][c] == num) return false;
    }

    //Box
    size_t row_begin = (row/3)*3;
    size_t col_begin = (col/3)*3;
    for(size_t r=0; r<3; ++r){
      for(size_t c=0; c<3; ++c){
        if(r+row_begin==row && c+col_begin==col) continue;
        if(m_board[r+row_begin][c+col_begin] == num) return false;
      }
    }

    return true;
  }

  bool solveSudoku(const size_t col){
    if(col == m_size) return true;

    //std::cout<<"checking col["<<col<<"]"<<std::endl;

    for(size_t row=0; row<m_size; ++row){
      //Already has number
      //std::cout<<"checking ["<<m_board[row][col]<<"]"<<std::endl;


      if(m_board[row][col] > 0) {
        if(canPlace(row,col,m_board[row][col])){
          continue;
        } else {
          return false;
        }
      }

      //Empty
      /*
      bool can_place = false;
      for(size_t i=0; i<=9; ++i){
        if(canPlace(row,col,i)){
          can_place = true;
          //m_board[row][col] = i;
          break;
        }
        //m_board[row][col] = 0;
      }

      if(!can_place) return false;
      */
    }

    return solveSudoku(col+1);
  }

public:
  /**
   * @param board: the board
   * @return: wether the Sudoku is valid
   */
  bool isValidSudoku(const vector<vector<char>>& board) {
    m_size = board.size();
    getBoard(board);
    return solveSudoku(0);
  }
};
```

### Subsets

Given a set of distinct integers, return all possible subsets.  
http://www.lintcode.com/en/problem/subsets/

```
class Solution {
public:
  /**
   * @param S: A set of numbers.
   * @return: A list of lists. All valid subsets.
   */
  vector<vector<int> > subsets(vector<int> &nums) {
    size_t subset_size = 1 << nums.size();

    vector<vector<int> > result;
    for(size_t i=0; i<subset_size; ++i){
      vector<int> subset;
      size_t cur_i = i;
      size_t idx = 0;
      while(cur_i){
        if(cur_i & 1) subset.push_back(nums[idx]);
        cur_i >>= 1;
        idx++;
      }
      result.push_back(subset);
    }

    return result;
  }
};
```

```

class Solution {
public:
  /**
   * @param S: A set of numbers.
   * @return: A list of lists. All valid subsets.
   */

  /*
    The set of subsets of {1} is {{}, {1}}
    For {1, 2}, take {{}, {1}}, add 2 to each subset to get {{2}, {1, 2}} and take the union with {{}, {1}} to get {{}, {1}, {2}, {1, 2}}
    Repeat till you reach n
  */

  vector<vector<int> > subsets(vector<int> &nums) {
    //Or initialize with 1 empty element
    //vector<vector<int> > result(1);
    vector<vector<int> > result(1);
    for(size_t i=0; i<nums.size(); ++i){
      size_t result_size = result.size();
      for(size_t s=0; s<result_size; ++s){
        result.push_back(result[s]);
        result.back().push_back(nums[i]);
      }
    }

    return result;
  }
};
```

### Subsets II
Given a list of numbers that may has duplicate numbers, return all possible subsets:  
Each element in a subset must be in non-descending order.  
The ordering between two subsets is free.  
The solution set must not contain duplicate subsets.  

http://www.lintcode.com/en/problem/subsets-ii/

```
#include <algorithm>
class Solution {
public:
  /**
   * @param S: A set of numbers.
   * @return: A list of lists. All valid subsets.
   */
  vector<vector<int>> subsetsWithDup(const vector<int> &S) {
    vector<int> sorted_s = S;
    std::sort(sorted_s.begin(),sorted_s.end());

    vector< vector<int> > result;
    result.push_back(vector<int>());

    size_t previous_size=0;
    for(size_t i=0; i<sorted_s.size(); ++i){
      size_t size = result.size();
      for(size_t s=0; s<size; ++s){

        //NOTE: not the first element && duplicates,
        //so avoid previous created elements, only add when s >= previous_size
        if(i>0 && sorted_s[i-1]==sorted_s[i] && s < previous_size ){
          continue;
        }

        result.push_back(result[s]);
        result.back().push_back(sorted_s[i]);
      }
      previous_size = size;
    }

    return result;
  }
};

/*
S = [1,2,2]
//result
[]

[1]

[2]
[1][2]

//NOTE: not the first element && duplicates,
//so avoid previous created elements, only add when s >= previous_size
//if(i>0 && sorted_s[i-1]==sorted_s[i] && s < previous_size )
//[2]
//[1][2]
[2][2]
[1][2][2]
*/
```
