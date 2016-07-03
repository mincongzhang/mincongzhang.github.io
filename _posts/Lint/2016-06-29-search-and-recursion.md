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
