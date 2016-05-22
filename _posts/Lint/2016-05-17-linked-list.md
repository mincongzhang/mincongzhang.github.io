---
layout: post
title: Play with Lintcode - Linked List
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

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

###  Merge Two Sorted Lists
Merge two sorted (ascending) linked lists and return it as a new sorted list. The new sorted list should be made by splicing together the nodes of the two lists and sorted in ascending order.
http://www.lintcode.com/en/problem/merge-two-sorted-lists/

```
#include <limits.h>
class Solution {
private:
  void append(ListNode * cur_node,const int & val){
    ListNode * next_node = new ListNode(val);
    if(cur_node == NULL){
        std::cout<<"cur: null"<<std::endl;
        return;
    } else {
        next_node->next = cur_node->next;
        cur_node->next = next_node;
    }
  }


public:
  ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {

    ListNode * l1_prehead = new ListNode(INT_MIN);
    l1_prehead->next = l1;

    ListNode * l1_it = l1_prehead;
    ListNode * l2_it = l2;

    while(l2_it != NULL){
      //NEXT == NULL

      if(l1_it->next == NULL){
        
        l1_it->next = l2_it;
        break;
      }

      //NEXT < L2_NODE
      if(l1_it->next->val < l2_it->val){
        if(l1_it->next->next == NULL){
          append(l1_it->next,l2_it->val);
          l2_it = l2_it->next;
        }
      //NEXT >= L2_NODE
      } else {
        append(l1_it,l2_it->val);
        l2_it = l2_it->next;
      }

      l1_it = l1_it->next;

    }

    return l1_prehead->next;
  }
};
```

###  Partition List
Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.
You should preserve the original relative order of the nodes in each of the two partitions.

http://www.lintcode.com/en/problem/partition-list/#

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
     * @param x: an integer
     * @return: a ListNode 
     */
    ListNode *partition(ListNode *head, int x) {
        ListNode * less_x = new ListNode(INT_MIN);
        ListNode * greater_equal_x = new ListNode(INT_MIN);
        
        ListNode * less_x_it = less_x;
        ListNode * greater_equal_x_it = greater_equal_x;
        
        while(head){
            if(head->val < x){
                less_x_it->next = head;
                less_x_it = less_x_it->next;
            } else {
                greater_equal_x_it->next = head;
                greater_equal_x_it = greater_equal_x_it->next;

            }

            head = head->next;
        }

        less_x_it->next = greater_equal_x->next;
        greater_equal_x_it->next = NULL; //NOTE: be careful
        
        ListNode * ret_list = less_x->next;
        delete less_x;
        delete greater_equal_x;


        return ret_list;
    }
};
```

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
     * @param x: an integer
     * @return: a ListNode 
     */
    ListNode *partition(ListNode *head, int x) {
        ListNode * less_x = new ListNode(INT_MIN);
        ListNode * greater_equal_x = new ListNode(INT_MIN);
        
        ListNode * less_x_it = less_x;
        ListNode * greater_equal_x_it = greater_equal_x;
        
        while(head){
            if(head->val < x){
                less_x_it->next = new ListNode(head->val);
                less_x_it = less_x_it->next;
            } else {
                greater_equal_x_it->next = new ListNode(head->val);
                greater_equal_x_it = greater_equal_x_it->next;
            }
            head = head->next;
        }
        
        less_x_it->next = greater_equal_x->next;
        ListNode * ret_list = less_x->next;
        delete less_x;
        delete greater_equal_x;

        return ret_list;
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
