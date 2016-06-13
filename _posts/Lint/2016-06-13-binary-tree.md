---
layout: post
title: Play with Lintcode - Binary Tree
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

###  Binary Tree Preorder Traversal
Given a binary tree, return the preorder traversal of its nodes' values.  
http://www.lintcode.com/en/problem/binary-tree-preorder-traversal/

```
//recursion
class Solution {
private:
  void preorder(TreeNode * root,vector<int> & list){
    if(root == NULL) return;
    list.push_back(root->val);
    preorder(root->left,list);
    preorder(root->right,list);
  }

public:
  /**
   * @param root: The root of binary tree.
   * @return: Preorder in vector which contains node values.
   */
  vector<int> preorderTraversal(TreeNode *root) {
    vector<int> preorder_list;
    preorder(root,preorder_list);
    return preorder_list;
  }
};
```

```
//stack
#include <stack>
class Solution {

public:
  /**
   * @param root: The root of binary tree.
   * @return: Preorder in vector which contains node values.
   */
  typedef TreeNode * TreeNodePtr;
  vector<int> preorderTraversal(TreeNode *root) {
    std::vector<int> val_list;
    if(root==NULL) return val_list;

    std::stack<TreeNodePtr> node_stack;
    node_stack.push(root);
    TreeNodePtr cur_node;

    while(!node_stack.empty()){
      cur_node = node_stack.top();
      node_stack.pop();
      val_list.push_back(cur_node->val);

      if(cur_node->right)
      node_stack.push(cur_node->right);
      if(cur_node->left)
      node_stack.push(cur_node->left);
    }

    return val_list;
  }
};
```

```
//visiting left stack
#include <stack>
class Solution {

public:
  /**
   * @param root: The root of binary tree.
   * @return: Preorder in vector which contains node values.
   */
  typedef TreeNode * TreeNodePtr;
  vector<int> preorderTraversal(TreeNode *root) {
    std::vector<int> val_list;
    std::stack<TreeNodePtr> node_stack;
    TreeNodePtr cur_node = root;

    while(cur_node){
      val_list.push_back(cur_node->val);

      if(cur_node->right){
        node_stack.push(cur_node->right);
      }

      if(cur_node->left){
        cur_node = cur_node->left;
        continue;
      }

      if(!node_stack.empty()){
        cur_node = node_stack.top();
        node_stack.pop();
        continue;
      }

      break;
    }//end while

    return val_list;
  }
};

```

```
//Morris Traversal
```
