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

