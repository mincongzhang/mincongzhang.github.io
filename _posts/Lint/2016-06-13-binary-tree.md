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
