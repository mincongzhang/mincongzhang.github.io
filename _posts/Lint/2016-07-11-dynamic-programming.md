---
layout: post
title: Play with Lintcode - Dynamic Programming
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

### Fibonacci

```
//Fibonacci: 1,1,2,3,5,8,13,21,34,55,89,144

#include <iostream>
#include <unordered_map>
#include <sstream>

int fib(int n){
    if(n==1 || n==2) return 1;
    
    return fib(n-1)+fib(n-2);
}

int fib2(int n){
    int cur = 1;
    int next = 1;
    int prev;
    while(--n > 0){
        prev = cur;
        cur = next;
        next = prev+cur;
    }
    
    return cur;
}

int fibHash(int n, std::unordered_map<int,int> & hash){
    if(n==1 || n==2) return 1;

    int result;
    
    std::unordered_map<int,int>::const_iterator it = hash.find(n);
    if(it != hash.end()){
        result = it->second;    
    } else {
        result = fib(n-1)+fib(n-2);
        hash[n] = result;
    }

    return result;
}

int main(){
    std::cout<<fib(10)<<std::endl;
    std::cout<<fib2(10)<<std::endl;

    std::unordered_map<int,int> hash;
    std::cout<<fibHash(10,hash)<<std::endl;
    std::cout<<fibHash(11,hash)<<std::endl;

    return 0;
}
```

### Pascal

```
/*

     1
    1 1
   1 2 1
  1 3 3 1
 1 4 6 4 1
1 5 10 10 5 1
*/

#include <iostream>
#include <vector>
#include <unordered_map>
#include <sstream>

namespace{
    template<typename T>
    std::string toString ( T num ){
        std::stringstream ss;
        ss << num;
        return ss.str();
    }
    
    std::string getHashKey(const int n1, const int n2){
        //TODO: can still optimise here, think about symetric key, should have the same value
        return toString(n1)+","+toString(n2);
    }
}

int computePascal( int row, int col )
{
    if(row<0 || !(col >=0 && col<=row)){
        return 0;
    }
    
    if(row == 0) return 1;
    if(col == 0 || col == row) return 1;
    
    return computePascal(row-1,col) + computePascal(row-1,col-1);
}

int computePascalHash( int row, int col, std::unordered_map<std::string,int> & hash){
    if(row<0 || !(col >=0 && col<=row)){
        return 0;
    }
    
    if(row == 0) return 1;
    if(col == 0 || col == row) return 1;
    
    int result(0);
    
    std::string hash_key = getHashKey(row,col);
    std::unordered_map<std::string,int>::const_iterator it = hash.find(hash_key);
    if(it!=hash.end()){
        result = it->second;
    } else {
        result = computePascal(row-1,col) + computePascal(row-1,col-1);
        hash[hash_key]=result;
    }
    
    return result;
}

int main(){
    std::cout<<computePascal(60,3)<<std::endl;
    
    std::unordered_map<std::string,int> hash;
    std::cout<<computePascalHash(60,3,hash)<<std::endl;
    return 0;
}
```

### Maximum Subarray
Given an array of integers, find a contiguous subarray which has the largest sum.  
http://www.lintcode.com/en/problem/maximum-subarray/  

```
//Solution1 O(n^2)
//For every element, go forward and find max, update global max

//Solution2 O(nlogn)
//Binary search, search left, and search right, and recursively solve the problem

//Solution3 O(n) update local max and global max at the same time
class Solution {
public:
  /**
   * @param nums: A list of integers
   * @return: A integer indicate the sum of max subarray
   */
  int maxSubArray(vector<int> nums) {
    if(nums.empty()) return 0;

    int local_max(INT_MIN),max(INT_MIN);
    for(size_t i=0; i<nums.size(); ++i){
      if(local_max<0){
        local_max = nums[i];
      } else {
        local_max += nums[i];
      }

      if(local_max > max){
        max = local_max;
      }
    }

    return max;
  }
};
```

###  Maximum Product Subarray
Find the contiguous subarray within an array (containing at least one number) which has the largest product.  
http://www.lintcode.com/en/problem/maximum-product-subarray/  

```
class Solution {
public:
  /**
   * @param nums: a vector of integers
   * @return: an integer
   */
  int maxProduct(vector<int>& nums) {
    int local_max(1),local_min(1),global_max(INT_MIN);
    for(size_t i=0; i<nums.size(); ++i){
    //Estimate max/min
      int est_max = local_max*nums[i];
      int est_min = local_min*nums[i];

      local_max  = std::max(nums[i],std::max(est_max,est_min));
      local_min  = std::min(nums[i],std::min(est_max,est_min));
      global_max = std::max(global_max,local_max);
    }

    return global_max;
  }
};
```

### Climbing Stairs
You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?  
http://www.lintcode.com/en/problem/climbing-stairs/  

```
class Solution {
public:
  /**
   * @param n: An integer
   * @return: An integer
   */
  int climbStairs(int n) {
    if(n<=0) return 0;
    if(n==1) return 1;
    if(n==2) return 2;
    return climbStairs(n-1) + climbStairs(n-2);
  }
};
```

```
class Solution {
public:
  /**
   * @param n: An integer
   * @return: An integer
   */
  int climbStairs(int n) {
    if(n<=0) return 1;
    if(n==1) return 1;
    if(n==2) return 2;

    std::vector<int> stairs(n,0);
    stairs[0] = 1;
    stairs[1] = 2;
    for(size_t i=2; i<n; ++i){
      stairs[i] = stairs[i-1] + stairs[i-2];
    }

    return stairs.back();
  }
};
```


### Triangle
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.  
http://www.lintcode.com/en/problem/triangle/  

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &A) {
        if(A.empty())     return 0;
        if(A.size() == 1) return A[0][0];
        
        const unsigned int row_num = A.size();
        unsigned int row,row_elem;
        
        //end at last-1 row
        for(row = 0; row < row_num-1; ++row){  
            //first elem of row+1
            A[row+1][0] += A[row][0];
            
            //start from 2nd element
            for(row_elem = 1; row_elem < A[row].size(); row_elem++){ 
                if(A[row][row_elem-1] < A[row][row_elem])
                    A[row+1][row_elem] += A[row][row_elem-1];
                else
                    A[row+1][row_elem] += A[row][row_elem];
            }
            
            //last elem of row+1
            A[row+1][A[row].size()] += A[row][A[row].size()-1];
        }
        
        //loop last row, get min
        vector<int> last_row = A[A.size()-1];
        int min = last_row[0];
        for(vector<int>::const_iterator i=last_row.begin();i!=last_row.end();++i){
            if(min > *i) min = *i;
        }
        
        return min;
    }
};
```

### Unique Paths

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
How many possible unique paths are there?

http://www.lintcode.com/en/problem/unique-paths/

```
class Solution {
public:
    /**
     * @param n, m: positive integer (1 <= n ,m <= 100)
     * @return an integer
     */
  int uniquePaths(int m, int n) {
    if(m <= 1 || n <= 1) return 1;

    std::vector<int> path_array(n,1);
    std::vector< std::vector<int> > path_map(m,path_array);

    for(int m_i=1; m_i<m; ++m_i){
      for(int n_i=1; n_i<n; ++n_i){
        path_map[m_i][n_i] = path_map[m_i-1][n_i]+path_map[m_i][n_i-1];
      }
    }

    return path_map.back().back();
  }
};

```

### Unique Paths II
Follow up for "Unique Paths":  
Now consider if some obstacles are added to the grids. How many unique paths would there be?  
An obstacle and empty space is marked as 1 and 0 respectively in the grid.  

```
class Solution {
public:
  /**
   * @param obstacleGrid: A list of lists of integers
   * @return: An integer
   */
  int uniquePathsWithObstacles(vector<vector<int> > &obstacleGrid) {
    if(obstacleGrid.empty()) return 0;
    if(obstacleGrid[0][0]==1) return 0;

    vector<int> array(obstacleGrid.front().size(),1); //initial with 1
    vector< vector<int> > path_map(obstacleGrid.size(),array);

    size_t col = array.size();
    size_t row = path_map.size();
    for(size_t r = 0; r<row; ++r){
      for(size_t c = 0; c<col; ++c){
        if(obstacleGrid[r][c]==1){
          path_map[r][c] = 0;
          continue;
        }

        if(r==0 && c==0) continue;

        if(r!=0 && c!=0){
          path_map[r][c] = path_map[r-1][c] + path_map[r][c-1];
          continue;
        }

        if(r==0){
          path_map[r][c] =  path_map[r][c-1];
        } else {
          path_map[r][c] =  path_map[r-1][c];
        }
      }
    }

    return path_map.back().back();
  }
};
```


### Unique Binary Search Trees
Given n, how many structurally unique BSTs (binary search trees) that store values 1...n?
http://www.lintcode.com/en/problem/unique-binary-search-trees/

reference:http://fisherlei.blogspot.co.uk/2013/03/leetcode-unique-binary-search-trees.html
```
这题想了好久才想清楚。其实如果把上例的顺序改一下，就可以看出规律了。
 1                1                      2                       3             3
    \                 \                 /      \                  /              / 
      3               2              1       3               2             1
    /                   \                                       /                  \
 2                       3                                   1                    2

比如，以1为根的树有几个，完全取决于有二个元素的子树有几种。同理，2为根的子树取决于一个元素的子树有几个。以3为根的情况，则与1相同。

定义Count[i] 为以[0,i]能产生的Unique Binary Tree的数目，

如果数组为空，毫无疑问，只有一种BST，即空树，
Count[0] =1

如果数组仅有一个元素{1}，只有一种BST，单个节点
Count[1] = 1

如果数组有两个元素{1,2}， 那么有如下两种可能
1                       2
  \                    /
    2                1
Count[2] = Count[0] * Count[1]   (1为根的情况)
                  + Count[1] * Count[0]  (2为根的情况。

再看一遍三个元素的数组，可以发现BST的取值方式如下：
Count[3] = Count[0]*Count[2]  (1为根的情况)
               + Count[1]*Count[1]  (2为根的情况)
               + Count[2]*Count[0]  (3为根的情况)

所以，由此观察，可以得出Count的递推公式为
Count[i] = ∑ Count[0...k] * [ k+1....i]     0<=k<i-1
问题至此划归为一维动态规划。
```

```
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: An integer
     */
    int numTrees(int n) {
        if(n<=1) return 1;
        
        std::vector<int> count(n+1,0);
        count[0] = 1;
        count[1] = 1;
        
        for(int i(2); i<count.size(); ++i){
            for(int j(0); j<i; ++j){
                count[i] += count[j]*count[i-j-1];
            }
        }
        
        return count.back();
    }
};
```


### Edit Distance
Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.) You have the following 3 operations permitted on a word:  
Insert a character  
Delete a character  
Replace a character  
http://www.lintcode.com/en/problem/edit-distance/  

```
class Solution {
private:
  int getMin(int n1,int n2,int n3){
    return std::min(n1,std::min(n2,n3));
  }

  int getMinDist(const string word1, const size_t word1_len, const string word2, const size_t word2_len){
    if(word1_len==0) return word2_len;//Insert all elements
    if(word2_len==0) return word1_len;//Erase  all elements

    //If last element equal
    if(word1[word1_len-1] == word2[word2_len-1]){
      return getMinDist(word1,word1_len-1,word2,word2_len-1);
    }

    //If not equal(insert/erase/replace)
    return 1+getMin(getMinDist(word1,word1_len,word2,word2_len-1),
                    getMinDist(word1,word1_len-1,word2,word2_len),
                    getMinDist(word1,word1_len-1,word2,word2_len-1));
  }
public:
  /**
   * @param word1 & word2: Two string.
   * @return: The minimum number of steps.
   */
  int minDistance(string word1, string word2) {
    return getMinDist(word1,word1.size(),word2,word2.size());
  }
};
```

### Word Break
Given a string s and a dictionary of words dict, determine if s can be break into a space-separated sequence of one or more dictionary words.  
e.g. Given s = "lintcode", dict = ["lint", "code"]. Return true because "lintcode" can be break as "lint code".  
http://www.lintcode.com/en/problem/word-break/  

```
//Basic solution with recursion
class Solution {
private:
  bool breaking(const std::string & s, const unordered_set<string> & dict, size_t begin){
    if(begin == s.size()) return true;

    for(size_t i=begin+1; i<=s.size(); ++i){
      std::string tmp_str = s.substr(begin,i-begin);
      if(dict.find(tmp_str)!=dict.end() && breaking(s,dict,i)){
        return true;
      }
    }
    return false;
  }

public:
  /**
   * @param s: A string s
   * @param dict: A dictionary of words dict
   */
  bool wordBreak(string s, unordered_set<string> &dict) {
    if(dict.empty() && s.empty()) return true;
    if(dict.empty()) return false;
    if(s.size()==1) return dict.find(s)!=dict.end();

    return breaking(s,dict,0);
  }
};
```

```
//Dynamic programming with recursion
#include <utility>//Pair
class Solution {
private:
  std::unordered_set<std::string> m_false_dict;
  bool breaking(const std::string & s, const unordered_set<string> & dict, size_t begin){
    if(begin == s.size()) return true;

    for(size_t i=begin+1; i<=s.size(); ++i){
      std::string tmp_str = s.substr(begin,i-begin);
      if(dict.find(tmp_str)!=dict.end() && m_false_dict.find(tmp_str)==m_false_dict.end()){
        if(breaking(s,dict,i)){
          return true;
        } else {
          m_false_dict.insert(tmp_str);
        }
      }
    }
    return false;
  }

public:
  /**
   * @param s: A string s
   * @param dict: A dictionary of words dict
   */
  bool wordBreak(string s, unordered_set<string> &dict) {
    if(dict.empty() && s.empty()) return true;
    if(dict.empty()) return false;
    if(s.size()==1) return dict.find(s)!=dict.end();

    return breaking(s,dict,0);
  }
};
```

```
//Basic solution without recursion
#include <utility>//Pair
class Solution {
private:
  //std::unordered_map<std::string,bool> m_false_dict;
  bool breaking(const std::string & s, const unordered_set<string> & dict){
    size_t begin = 0;
    std::queue<size_t> Q;
    std::unordered_set<size_t> false_begin;
    Q.push(begin);

    while(!Q.empty()){
      size_t begin_i = Q.front();
      Q.pop();

      for(size_t i=begin_i+1; i<=s.size(); ++i){
        std::string tmp_str = s.substr(begin_i,i-begin_i);
        if(dict.find(tmp_str)!=dict.end()){
            if(i==s.size()){
                return true;
            } else {
                Q.push(i);
            }
        }
      }//for
    }//while

    return false;
  }

public:
  /**
   * @param s: A string s
   * @param dict: A dictionary of words dict
   */
  bool wordBreak(string s, unordered_set<string> &dict) {
    if(dict.empty() && s.empty()) return true;
    if(dict.empty()) return false;
    if(s.size()==1) return dict.find(s)!=dict.end();

    return breaking(s,dict);
  }
};
```

```
//Dynamic programming without recursion, checking max dict word length
#include <utility>//Pair
#include <algorithm>//max

class Solution {
private:
  bool breaking(const std::string & s, const unordered_set<string> & dict){
    size_t max_len=0;
    for(unordered_set<string>::const_iterator i=dict.begin();i!=dict.end();++i){
      max_len = std::max(max_len,i->size());
    }

    size_t begin = 0;
    std::queue<size_t> Q;
    std::unordered_set<size_t> false_begins;
    Q.push(begin);

    while(!Q.empty()){
      size_t begin_i = Q.front();
      Q.pop();
      if(false_begins.find(begin_i)!=false_begins.end()) {continue;}

      for(size_t i=begin_i+1; i<=s.size()&&i-begin_i<=max_len; ++i){
        std::string tmp_str = s.substr(begin_i,i-begin_i);
        if(dict.find(tmp_str)!=dict.end()){
          if(i==s.size()){
            return true;
          } else {
            false_begins.insert(begin_i);
            Q.push(i);
          }
        }
      }//for
    }//while

    return false;
  }

public:
  /**
   * @param s: A string s
   * @param dict: A dictionary of words dict
   */
  bool wordBreak(string s, unordered_set<string> &dict) {
    if(dict.empty() && s.empty()) return true;
    if(dict.empty()) return false;
    if(s.size()==1) return dict.find(s)!=dict.end();

    return breaking(s,dict);
  }
};
```

### Distinct Subsequences
Given a string S and a string T, count the number of distinct subsequences of T in S.

A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ACE" is a subsequence of "ABCDE" while "AEC" is not).  

http://www.lintcode.com/en/problem/distinct-subsequences/  

```
//e.g.
//S: xxxabcxxxabc T:abc, awnser is 4
//we can do:
//---abc------
//---------abc
//---a------bc
//---ab------c
```

```
//recursive solution

class Solution {

private:
  std::string m_S;
  std::string m_T;

  int getNumDistinct(size_t s_start, size_t t_start){
    //When there is no T left, we have 1 distinct subsequence
    if(t_start==m_T.size()){
      return 1;
    }

    if(s_start == m_S.size()){
      //when S is empty but we still have T, means we have no distinct subsequences
      return 0;
    }

    int num = 0;
    for(size_t s=s_start; s<m_S.size(); ++s){
      if(m_S[s]==m_T[t_start]){
        num += getNumDistinct(s+1,t_start+1);
      }
    }

    return num;
  }//getNumDistinct

public:
  /**
   * @param S, T: Two string.
   * @return: Count the number of distinct subsequences
   */
  int numDistinct(string &S, string &T) {
    m_S = S;
    m_T = T;
    return getNumDistinct(0,0);
  }
};
```

```
//Dynamic programming space O(T*S)
//e.g.
//S: xxxabcxxxabc T:abc, awnser is 4
//we can do:
//---abc------
//---ab------c
//---a------bc
//---------abc

//DP:
//  x x x a b c x x x a b c
//a - - - - - - - - - - - - 0
//b - - - - - - - - - - - - 0
//c - - - - - - - - - - - - 0
//  1 1 1 1 1 1 1 1 1 1 1 1 1

//start from bottom right
//if (S[i] == T[j]) {
//    N[i][j] = N[i+1][j] + N[i+1][j+1];
//} else {
//    N[i][j] = N[i+1][j];
//}

//DP:
//  x x x a b c x x x a b c
//a 4 4 4 4 1 1 1 1 1 1 0 0 0
//b 3 3 3 3 3 1 1 1 1 1 1 0 0
//c 2 2 2 2 2 2 1 1 1 1 1 1 0
//  1 1 1 1 1 1 1 1 1 1 1 1 1
```
```
Dynamic programming space O(T*S)
/*
Example
  Given S = "rabbbit", T = "rabbit", return 3.

    r a b b b i t S
  1 1 1 1 1 1 1 1
r 0 1 1 1 1 1 1 1
a 0 0 1 1 1 1 1 1
b 0 0 0 1 2 3 3 3
b 0 0 0 0 1 3 3 3
i 0 0 0 0 0 0 3 3
t 0 0 0 0 0 0 0 3
T
*/
//if (!=) =left (inherit the previous count)
//if (==) =left+upper_left, it means if equal, add the result of previous compare [x-1][y-1]/*

class Solution {

public:
  /**
   * @param S, T: Two string.
   * @return: Count the number of distinct subsequences
   */
  int numDistinct(string &S, string &T) {
    //count[s][t]
    vector<int> T_count(T.size()+1,0);
    vector< vector<int> > count(S.size()+1,T_count);

    for(size_t s=0; s<count.size();++s){
      count[s][0]=1;
    }

    for(size_t s=0; s<S.size();++s){
      for(size_t t=0; t<T.size();++t){
        if(S[s]!=T[t]){
          count[s+1][t+1]=count[s][t+1];
        } else {
          count[s+1][t+1]=count[s][t+1]+count[s][t];
        }
      }
    }

    return count.back().back();
  }
};
```

```
//Dynamic programming, space O(T)
/*
   r a b b b i t S
   1 1 1 1 1 1 1
r  1 1 1 1 1 1 1
a  0 1 1 1 1 1 1
b  0 0 1 2 3 3 3
b  0 0 0 1 3 3 3
i  0 0 0 0 0 3 3
t  0 0 0 0 0 0 3
T
*/
class Solution {

public:
  /**
   * @param S, T: Two string.
   * @return: Count the number of distinct subsequences
   */
  int numDistinct(string &S, string &T) {
    std::vector<int> count(T.size()+1,0);
    count[0]=1;

    for(int s=0; s<S.size();++s){
      //Why backward here? In this way you could get previous compare results without overwrite them
      for(int t=T.size()-1; t>=0;--t){
        if(S[s]==T[t]){
          count[t+1]+=count[t];
        }
      }
    }

    return count.back();
  }
};

//The same if you go the other way round
/*
   r a b b b i t S
r  3 0 0 0 0 0 0
a  3 3 0 0 0 0 0
b  3 3 3 3 1 0 0
b  2 2 2 2 1 0 0
i  2 1 1 1 1 1 0
t  1 1 1 1 1 1 1
   1 1 1 1 1 1 1
T
*/

```


### Backpack

Given n items with size Ai, an integer m denotes the size of a backpack. How full you can fill this backpack?

http://www.lintcode.com/en/problem/backpack/

```
//Dynamic programming

#define log(msg) do{ std::cout<<msg<<"\n"; }while(0)


template <typename T>
inline std::ostream& operator<< (std::ostream &os, const std::vector<T> & value ) {
    os<<"\n";
  for(typename std::vector<T>::const_iterator it=value.begin(); it!=value.end(); ++it){
    os<<"["<<*it<<"]";
  }
  return os;
}


class Solution {
public:
  /**
   * @param m: An integer m denotes the size of a backpack
   * @param A: Given n items with size A[i]
   * @return: The maximum size
   */
  int backPack(int m, std::vector<int> A) {
    //  mmmmmmmmmmmmm
    //0
    //A
    //A
    //A

    //NOTE: weight map should go from 0 to max weight
    std::vector<int> item_pick(m+1,0);
    std::vector< std::vector<int> > pack(A.size()+1,item_pick);

    for(int item=1;item<=A.size();++item){
      for(int weight=0;weight<=m;++weight){

        //NOTE: do this just in case you get A[item], and that's wrong!
        int item_idx = item-1;

        //weight exceed
        if(weight-A[item-1]<0){
          pack[item][weight] = pack[item-1][weight];
          continue;
        }

        int added_weight = pack[item-1][weight-A[item_idx]]+A[item_idx];
        if(added_weight > weight){
          //cannot take
          pack[item][weight] = pack[item-1][weight];
        } else {
          //can take
          if(added_weight > pack[item-1][weight]){
          pack[item][weight] = added_weight;
          } else {
            pack[item][weight] = pack[item-1][weight];
          }
        }
      }//weight loop
    }//item loop

    return pack.back().back();
  }
};
```

```
//Recursive

class Solution {
public:
  /**
   * @param m: An integer m denotes the size of a backpack
   * @param A: Given n items with size A[i]
   * @return: The maximum size
   */
  int getWeight(const std::vector<int> & A,size_t item, const int remaining_weight){
    if(item >= A.size()){ return 0; }

    //not taking current item
    int weight = getWeight(A,item+1,remaining_weight);
    if(remaining_weight-A[item] < 0){
      return weight;
    }

    return std::max(weight,getWeight(A,item+1,remaining_weight-A[item])+A[item]);
  }

  int backPack(int m, std::vector<int> A) {
    return getWeight(A,0,m);
  }
};
```

### Interleaving String
Given three strings: s1, s2, s3, determine whether s3 is formed by the interleaving of s1 and s2.  
http://www.lintcode.com/en/problem/interleaving-string/

```
class Solution {
public:
  /**
   * Determine whether s3 is formed by interleaving of s1 and s2.
   * @param s1, s2, s3: As description.
   * @return: true of false.
   */
  bool isInterleave(string s1, string s2, string s3) {
    size_t s1_it=0;
    size_t s2_it=0;
    size_t s3_it=0;

    for(;s3_it<s3.size();++s3_it){
      if(s1_it<s1.size()){
        if(s1[s1_it] == s3[s3_it]){
          s1_it++;
          continue;
        }
      }

      if(s2_it<s2.size()){
        if(s2[s2_it] == s3[s3_it]){
          s2_it++;
          continue;
        }
      }

      break;
    }

    return (s1_it==s1.size() && s2_it==s2.size() && s3_it==s3.size());
  }
};
```
