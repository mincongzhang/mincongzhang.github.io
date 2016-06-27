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


### Balanced Binary Tree
Given a binary tree, determine if it is height-balanced.
For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

http://www.lintcode.com/en/problem/balanced-binary-tree/

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
#include <stdlib.h>
class Solution {
  bool checkBalance(TreeNode *root,int & depth){
    if(root==NULL) return true;

    int left_depth(depth),right_depth(depth);
    if(checkBalance(root->left,left_depth) && checkBalance(root->right,right_depth)){
      if(std::abs(left_depth-right_depth) > 1) return false;

      depth = (left_depth>right_depth? left_depth:right_depth)+1;
      return true;
    }

    return false;
  }


public:
  /**
   * @param root: The root of binary tree.
   * @return: True if this Binary tree is Balanced, or false.
   */
  bool isBalanced(TreeNode *root) {
    int depth(0);
    return checkBalance(root,depth);
  }
};
```

### Insert Node in a Binary Search Tree
Given a binary search tree and a new tree node, insert the node into the tree. You should keep the tree still be a valid binary search tree.

http://www.lintcode.com/en/ladder/

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
  /**
   * @param root: The root of the binary search tree.
   * @param node: insert this node into the binary search tree
   * @return: The root of the new binary search tree.
   */
  TreeNode* insertNode(TreeNode* root, TreeNode* node) {
    if(node == NULL) return root;
    if(root == NULL) return node;

    TreeNode* cur_node = root;
    while(true){
      //> current node
      if(node->val > cur_node->val){
        if(cur_node->right){
          cur_node = cur_node->right;
        } else {
          cur_node->right = node;
          break;
        }
        //<= current node
      } else {
        if(cur_node->left){
          cur_node = cur_node->left;
        } else {
          cur_node->left = node;
          break;
        }
      }
    }

    return root;
  }
};
```

###  Maximum Depth of Binary Tree
Given a binary tree, find its maximum depth.
The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

http://www.lintcode.com/en/problem/maximum-depth-of-binary-tree/

```
class Solution {
public:
  /**
   * @param root: The root of binary tree.
   * @return: An integer
   */
  int maxDepth(TreeNode *root) {
    if(root == NULL) return 0;

    int left_depth = maxDepth(root->left);
    int right_depth = maxDepth(root->right);

    return ( left_depth > right_depth ? left_depth : right_depth )+1;
  }
};
```

### Search Range in Binary Search Tree
Given two values k1 and k2 (where k1 < k2) and a root pointer to a Binary Search Tree. Find all the keys of tree in range k1 to k2. i.e. print all x such that k1<=x<=k2 and x is a key of given BST. Return all the keys in ascending order.

http://www.lintcode.com/en/problem/search-range-in-binary-search-tree/

```
class Solution {
private:
  void search(TreeNode* root, const int & k1, const int & k2, std::vector<int> & out){
    if(root==NULL) return;

    search(root->left,k1,k2,out);

    if(root->val >= k1 && root->val <= k2 ){
      out.push_back(root->val);
    }

    search(root->right,k1,k2,out);
  }

public:
  /**
   * @param root: The root of the binary search tree.
   * @param k1 and k2: range k1 to k2.
   * @return: Return all keys that k1<=key<=k2 in ascending order.
   */
  vector<int> searchRange(TreeNode* root, int k1, int k2) {
    std::vector<int> out;
    search(root,k1,k2,out);
    return out;
  }
};
```

### Binary Tree Level Order Traversal
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).
http://www.lintcode.com/en/problem/binary-tree-level-order-traversal/

```
#include <queue>

class Solution {

  /**
   * @param root: The root of binary tree.
   * @return: Level order a list of lists of integer
   */
typedef TreeNode * TreeNodePtr;
public:
  vector<vector<int>> levelOrder(TreeNode *root) {
    vector<vector<int>> out;
    if(root == NULL) return out;

    std::queue<TreeNodePtr> Q;
    Q.push(root);

    while(!Q.empty()){
      unsigned int q_size = Q.size();

      std::vector<int> level_values;
      while(q_size--){
        TreeNodePtr cur_node = Q.front();
        Q.pop();
        level_values.push_back(cur_node->val);

        if(cur_node->left) Q.push(cur_node->left);
        if(cur_node->right) Q.push(cur_node->right);

      }
      out.push_back(level_values);
    }

    return out;
  }
};
```

### Validate Binary Search Tree
Given a binary tree, determine if it is a valid binary search tree (BST).
http://www.lintcode.com/en/problem/validate-binary-search-tree/
*key point: need to check min,max range for each node. 


```
class Solution {
  typedef TreeNode * TreeNodePtr;
private:
  bool isValidBST(TreeNodePtr left,TreeNodePtr root,TreeNodePtr right,int min, int max){
    if(root==NULL) return true;

    //NOTE: important here
    if(root->val < min || root->val > max){
        return false;
    }

    bool valid_left = true;
    if(left){
      valid_left = (left->val < root->val) && isValidBST(left->left,left,left->right,min,root->val);
    }

    bool valid_right = true;
    if(right){
      valid_right = (right->val > root->val) && isValidBST(right->left,right,right->right,root->val,max);
    }

    return valid_left&&valid_right;
  }

public:
  /**
   * @param root: The root of binary tree.
   * @return: True if the binary tree is BST, or false
   */
  bool isValidBST(TreeNode *root) {
    if(root == NULL) return true;
    return isValidBST(root->left,root,root->right,INT_MIN,INT_MAX);
  }
};
```

### Construct Binary Tree from Preorder and Inorder Traversal
Given preorder and inorder traversal of a tree, construct the binary tree.
http://www.lintcode.com/en/problem/construct-binary-tree-from-preorder-and-inorder-traversal/

```
 #include <unordered_map>
 #include <utility>

class Solution {
    /**
     *@param preorder : A list of integers that preorder traversal of a tree
     *@param inorder : A list of integers that inorder traversal of a tree
     *@return : Root of a tree
     */

typedef TreeNode * TreeNodePtr; 
private:
    std::unordered_map<int,size_t> m_inorder_map;
    vector<int> m_preorder, m_inorder;
    size_t m_pre_it;
    TreeNodePtr build(const size_t & begin, const size_t & end){
        if(m_pre_it>=m_preorder.size() || begin>=end) return NULL;
        
        int current_pre_val = m_preorder[m_pre_it];
        m_pre_it++;
        
        TreeNodePtr node = new TreeNode(current_pre_val);
        node->left = build(begin,m_inorder_map[current_pre_val]);
        node->right = build(m_inorder_map[current_pre_val]+1,end);
        return node;
    }
    
public:
    TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder) {
        for(size_t i(0);i<inorder.size();++i){
            m_inorder_map.insert(std::pair<int,size_t>(inorder[i],i));
        }
        
        m_preorder = preorder;
        m_inorder  = inorder;
        m_pre_it   = 0;
        
        return build(0,preorder.size());
    }
};
```

### Binary Tree Serialization
Design an algorithm and write code to serialize and deserialize a binary tree. Writing the tree to a file is called 'serialization' and reading back from the file to reconstruct the exact same binary tree is 'deserialization'.

There is no limit of how you deserialize or serialize a binary tree, you only need to make sure you can serialize a binary tree to a string and deserialize this string to the original structure.

http://www.lintcode.com/en/problem/binary-tree-serialization/


```
#include <string>

namespace {
  const char DELIMITER = '#';
}

class Solution {
  typedef TreeNode * TreeNodePtr;

public:
  /**
   * This method will be invoked first, you should design your own algorithm
   * to serialize a binary tree which denote by a root node to a string which
   * can be easily deserialized by your own "deserialize" method later.
   */
  string serialize(TreeNodePtr root) {
    std::string data;
    serialize(root,data);
    return data;
  }

  /**
   * This method will be invoked second, the argument data is what exactly
   * you serialized at method "serialize", that means the data is not given by
   * system, it's given by your own serialize method. So the format of data is
   * designed by yourself, and deserialize it here as you serialize it in
   * "serialize" method.
   */
  TreeNodePtr deserialize(string data) {
    size_t start(0);
    return deserialize(data,start);
  }

private:
  void serialize(TreeNodePtr root,std::string & data){
    if(root == NULL){
      data += DELIMITER;
      data += " ";
      return;
    }

    data += std::to_string(root->val)+" ";
    serialize(root->left,data);
    serialize(root->right,data);
  }

  TreeNodePtr deserialize(const std::string & data,size_t & start){
    int num(0);
    if(!getNumber(data,start,num)){
      return NULL;
    }

    TreeNodePtr node = new TreeNode(num);
    node->left  = deserialize(data,start);
    node->right = deserialize(data,start);
    return node;
  }

  bool getNumber(const std::string & data, size_t & start, int & num){
    //if(start >= data.size()) return false;

    if(data[start] == DELIMITER){
      start += 2; //DELIMITER + " "
      return false;
    }

    int sign = 1;
    if(data[start] == '-'){
      sign = -1;
      start++;
    }

    for (num = 0; isdigit(data[start]); ++(start)) {
      num = num * 10 + data[start] - '0';
    }
    start++;// skip " "

    num *= sign;
    return true;
  }

};
```

### Remove Node in Binary Search Tree
Given a root of Binary Search Tree with unique value for each node. Remove the node with given value. If there is no such a node with given value in the binary search tree, do nothing. You should keep the tree still a binary search tree after removal.

http://www.lintcode.com/en/problem/remove-node-in-binary-search-tree/

```
#include <algorithm>
class Solution {
  typedef TreeNode * TreeNodePtr;

private:
  void removeEqual(TreeNodePtr & root){
    if(root->right == NULL){
      TreeNodePtr tmp = root;
      root = root->left;
      delete tmp;
      return;
    }

    if(root->right->left == NULL){
      std::swap(root->right->val,root->val);
      TreeNodePtr tmp = root->right;
      root->right = root->right->right;
      delete tmp;
      return;
    }

    TreeNodePtr most_left_parent = root->right;
    TreeNodePtr most_left = root->right->left;
    while(most_left->left){
      most_left_parent = most_left_parent->left;
      most_left = most_left->left;
    }

    std::swap(most_left->val,root->val);
    most_left_parent->left = most_left->right;
    delete most_left;
  }

  void remove(TreeNodePtr & root, const int value){
    if(root == NULL) return;

    if(root->val == value){
      removeEqual(root);
      return;
    }

    if(root->val > value){
      remove(root->left,value);
    } else {
      remove(root->right,value);
    }
  }

public:
  /**
   * @param root: The root of the binary search tree.
   * @param value: Remove the node with given value.
   * @return: The root of the binary search tree after removal.
   */
  TreeNode* removeNode(TreeNode* root, int value) {
    remove(root,value);
    return root;
  }
};

```
