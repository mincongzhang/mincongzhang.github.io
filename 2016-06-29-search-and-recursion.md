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
