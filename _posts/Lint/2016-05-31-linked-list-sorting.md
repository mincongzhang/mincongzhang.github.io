---
layout: post
title: Play with Lintcode - Linked List - Sorting List
category: Lint
description: Lintcode
tags: ["C++","算法"]
---

NOTE: what I learnt in lists:  
Either in partition or merging, don't need to do the in place swap shit. Just create a new list node and append to the end of it.



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
        //Idea:
        //create 2 lists with less/greater, appending
        //In the end conect 2 lists
        
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
//General Partition solution
class Solution {
private:
  void swap(ListNode * node1,ListNode * node2){
    if(node1==NULL || node2==NULL){return;}

    int tmp = node1->val;
    node1->val = node2->val;
    node2->val = tmp;
  }

public:
  ListNode *partition(ListNode *head, int x) {
    ListNode * list_it = head;
    ListNode * node_to_update = NULL;
    while(list_it != NULL){

      // >= x
      if(list_it->val >= x){
        if(node_to_update == NULL){
          node_to_update = list_it;
        }

      }
      // < x
      else {
        if(node_to_update != NULL){
          swap(node_to_update,list_it);
          if(node_to_update->next && node_to_update->next->val < x){
            node_to_update = NULL;
          } else {
            node_to_update = node_to_update->next;
          }
        }
      }

      list_it = list_it->next;
    }

    return head;
  }
};
```


#  Sort List
Sort a linked list in O(n log n) time using constant space complexity

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
void swap(ListNodePtr & node1, ListNodePtr & node2){
    if(node1 == NULL || node2 == NULL) return;
    
    int tmp_val = node1->val;
    node1->val = node2->val;
    node2->val = tmp_val;
}

void qsort(ListNodePtr begin, ListNodePtr end){
    if(begin == end) return;
    if(begin->next == NULL) return;
    
    //Handle 2 Nodes
    if(begin->next == end){
        if(begin->val >= end->val){
            swap(begin,end);
        }
        return;
    }
    
    ListNodePtr iter = begin;
    int compare_val = iter->val;

    ListNodePtr mid = begin;
    ListNodePtr larger = NULL;

    while(iter != end){
        // iter >= begin
        if(iter->val >= compare_val){
            if(larger == NULL){
                larger = iter;
            }
        } else {
            // iter < begin
            if(larger != NULL){
                mid = larger;
                swap(larger,iter);
                if(larger->next != NULL && larger->next->val >= compare_val){
                    larger = larger->next;
                } else {
                    larger = NULL;
                }
            }
        }
        iter = iter->next;
    }
    
    //Handle End
    if(end){
        if(end->val <= compare_val){
            mid = mid->next;
            swap(end,mid);
        }
    }
    
    qsort(begin, mid);
    qsort(mid->next,end);
}
    
public:
    /**
     * @param head: The first node of linked list.
     * @return: You should return the head of the sorted linked list,
                    using constant space complexity.
     */
    ListNode *sortList(ListNode *head) {
        qsort(head,NULL);
        
        return head;
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
private:
  ListNode * merge(ListNode * head1, ListNode * head2){
    ListNode pre_head(INT_MIN);
    pre_head.next = NULL;
    ListNode * pre_head_iter = &pre_head;

    while(head1 && head2){
      if(head1->val > head2->val){
        pre_head_iter->next = head2;
        head2 = head2->next;
      } else {
        pre_head_iter->next = head1;
        head1 = head1->next;
      }
      pre_head_iter = pre_head_iter->next;
    }

    if(head1){
      pre_head_iter->next = head1;
    } else {
      pre_head_iter->next = head2;
    }

    return pre_head.next;
  }

public:
  /**
   * @param head: The first node of linked list.
   * @return: You should return the head of the sorted linked list,
                    using constant space complexity.
  */
  ListNode *sortList(ListNode *head) {
    if(head == NULL || head->next == NULL) return head;

    ListNode * fast = head;
    ListNode * mid  = head;
    //NOTE: should check fast->next first, then fast->next->next
    while(fast->next != NULL && fast->next->next != NULL){
      mid = mid->next;
      fast = fast->next->next;
    }

    //Split
    ListNode * mid_head = mid->next;
    mid->next = NULL;
    return merge(sortList(head),sortList(mid_head));
  }
};
```
