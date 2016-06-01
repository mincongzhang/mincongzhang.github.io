---
layout: post
title: Play with Lintcode - Linked List
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

NOTE: what I learnt in lists:  
Either in partition or merging, don't need to do the in place swap shit. Just create a new list node and append to the end of it.


### Remove Nth Node From End of List
Given a linked list, remove the nth node from the end of list and return its head.
http://www.lintcode.com/en/problem/remove-nth-node-from-end-of-list/

```
class Solution {
public:
  /**
   * @param head: The first node of linked list.
   * @param n: An integer.
   * @return: The head of linked list.
   */
  ListNode *removeNthFromEnd(ListNode *head, int n) {
    //Why use pre_head?
    //e.g.
    //list: 1->null, n=1
    //how to delete 1?
    ListNode * pre_head = new ListNode(0);
    pre_head->next = head;

    ListNode * n_ahead = pre_head;
    ListNode * n_node  = pre_head;

    //move: n_ahead
    while(n--){
      if(n_ahead){
        n_ahead = n_ahead->next;
      } else {
        std::cout<<"unhandled list with n ["<<n<<"]"<<std::endl;
        return pre_head;
      }
    }

    //n_ahead->next point to null
    while(n_ahead->next){
      n_ahead = n_ahead->next;
      n_node = n_node->next;
    }

    //delete n_node->next
    ListNode * delete_node = n_node->next;
    n_node->next = delete_node->next;
    delete delete_node;

    return pre_head->next;
  }
};
```

###  Remove Duplicates from Sorted List
Given a sorted linked list, delete all duplicates such that each element appear only once.
http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-list/

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @return: head node
     */
    ListNode *deleteDuplicates(ListNode *head) {
        if(head==NULL || head->next==NULL) return head;
        
        ListNode * next_tmp;
        ListNode * cur_tmp(head);
        while(cur_tmp != NULL && cur_tmp->next!=NULL){
            
            if(cur_tmp->val == cur_tmp->next->val){
                next_tmp = cur_tmp->next->next;
                delete cur_tmp->next;
                cur_tmp->next = next_tmp;
            } else {
                cur_tmp = cur_tmp->next;
            }
        }
        
        return head;
    }
};
```

### Reverse Linked List
Reverse a linked list.

```
/**
 * Definition of ListNode
 * 
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 * 
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
  private:
  void insert(ListNode * cur_node, const int & val){
    ListNode * insert_node = new ListNode(val);
    insert_node->next = cur_node->next;
    cur_node->next = insert_node;
  }
  
  public:
  ListNode *reverse(ListNode *head){
    if(head == NULL) return NULL;
  
    ListNode * new_head = new ListNode(INT_MIN);
    while(head != NULL){
      insert(new_head,head->val);
      head = head->next;
    }
    return new_head->next;
  }
};

```

###  Linked List Cycle
Given a linked list, determine if it has a cycle in it.
http://www.lintcode.com/en/problem/linked-list-cycle/

```
//Accepted solution
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @return: True if it has a cycle, or false
     */
    bool hasCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;

        while (fast && fast->next) {
            slow = slow->next, fast = fast->next->next;
            if (slow == fast) {  // There is a cycle.
                return true;
            }
        }
        return false;  // No cycle.
    }
};
```

```
//My Solution
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @return: True if it has a cycle, or false
     */
    bool hasCycle(ListNode *head) {
        if(head == NULL) return false;
        
        ListNode * list_duplicate = head;
        
        head = head->next;
        while(head){
            if(head->val == list_duplicate->val){
                if(head == list_duplicate){
                    return true;
                }
                
                list_duplicate = head;
            }
            
            head = head->next;
        }
        
        return false;
    }
};
```

### Rotate List
Given a list, rotate the list to the right by k places, where k is non-negative.  
http://www.lintcode.com/en/problem/rotate-list/

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
  /**
   * @param head: the list
   * @param k: rotate to the right k places
   * @return: the list after rotation
   */
  ListNode *rotateRight(ListNode *head, int k) {
    if(k == 0) return head;
    if(head == NULL || head->next == NULL) return head;

    //NOTE: size should start from 1
    int size = 1;
    ListNode * list_it = head;
    while(list_it->next != NULL){
      list_it = list_it->next;
      size++;
    }
    list_it->next = head;

    //NOTE: should be k%size rather than size%k
    int tail_pos = size - k % size;
    ListNode * rotate_tail = head;
    while(--tail_pos > 0){
      rotate_tail = rotate_tail->next;
    }

    ListNode * rotate_head = rotate_tail->next;
    rotate_tail->next = NULL;

    return rotate_head;
  }
};
```

### Reorder List
Given a singly linked list L: L0 → L1 → … → Ln-1 → Ln
reorder it to: L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …

http://www.lintcode.com/en/problem/reorder-list/

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
typedef ListNode * ListNodePtr;
    
private:
  ListNode * reverse(ListNode * head){
    ListNode reverse_head(INT_MIN);
    ListNode * reverse_head_iter = &reverse_head;

    while(head){
        ListNode * head_next = head->next;
        head->next = reverse_head_iter->next;
        reverse_head_iter->next = head;
        head = head_next;
    }

    return reverse_head.next;
  };
  
  void append(ListNodePtr & node, ListNodePtr & append_node){
      if(!node || !append_node) return;
      
      node->next = append_node;
      node = node->next;
      append_node = append_node->next;
  }

  ListNode * merge(ListNode * head1, ListNode * head2){
      ListNode pre_head(INT_MIN);
      ListNode * pre_head_iter = &pre_head;
      
      while(head1 && head2){
          append(pre_head_iter,head1);
          append(pre_head_iter,head2);
      }
      
      if(head1){
          append(pre_head_iter,head1);
      } 
      if(head2){
          append(pre_head_iter,head2);
      }
      
      return pre_head.next;
  }

public:
  /**
   * @param head: The first node of linked list.
   * @return: void
   */
  void reorderList(ListNode *head) {
    if(head == NULL || head->next == NULL) return;

    ListNode * ahead = head;
    ListNode * mid   = head;
    while(ahead->next && ahead->next->next){
      mid = mid->next;
      ahead = ahead->next->next;
    }

    ListNode * mid_head = mid->next;
    mid->next = NULL;
    merge(head,reverse(mid_head));
  }
};
```

###  Convert Sorted List to Balanced BST
Given a singly linked list where elements are sorted in ascending order, convert it to a height balanced BST.

```
//O(n) solution

class Solution {
typedef ListNode * ListNodePtr;
typedef TreeNode * TreeNodePtr;
private:
  TreeNodePtr buildBST(ListNodePtr & L, const int & begin, const int & end){
    if(begin >= end) return NULL;

    int mid = (begin+end)/2;

    TreeNodePtr left_sub_tree = buildBST(L,begin,mid);//NOTE: L will get updated inside(pass by reference)
    TreeNodePtr root = new TreeNode(L->val);
    L = L->next;
    TreeNodePtr right_sub_tree = buildBST(L,mid+1,end);

    root->left = left_sub_tree;
    root->right = right_sub_tree;
    return root;
  }

public:
  /**
   * @param head: The first node of linked list.
   * @return: a tree node
   */
  TreeNode *sortedListToBST(ListNode *L) {
    int list_size = 0;
    ListNodePtr list_iter = L;
    while(list_iter){
      list_size++;
      list_iter = list_iter->next;
    }

    return buildBST(L,0,list_size);
  }
};
```
