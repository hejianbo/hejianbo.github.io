---
layout: post
title: 每日一题 - 328.Odd Even Linked List
tags:
- leetcode
- 链表
- 双指针
categories: leetcode
description: LeetCode 328.Odd Even Linked List
---


# 328.Odd Even Linked List

https://leetcode.com/problems/odd-even-linked-list/

## Description

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

**Example 1:**

```
Input: 1->2->3->4->5->NULL
Output: 1->3->5->2->4->NULL
```

**Example 2:**

```
Input: 2->1->3->5->6->4->7->NULL
Output: 2->3->6->7->1->5->4->NULL
```

**Note:**

- The relative order inside both the even and odd groups should remain as it was in the input.
- The first node is considered odd, the second node even and so on ...



## Solution 

链表问题没啥说的, 画上图一下就出来了.

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode oddEvenList(ListNode head) {                
        if (head == null || head.next == null) {
            return head;
        }
        
        
        ListNode odd = head;        
        ListNode prev = head.next;
        ListNode p = head.next.next;        
        int i = 3;
        
        while (p != null) {     
            ListNode current = p;
            ListNode next = p.next;
            // 移动奇数位的数字
            if (i % 2 == 1) {
                // 移除当前结点               
                current.next = null;
                prev.next = next;
                prev = prev.next;
                // 将移除的结点放到odd后面
                current.next = odd.next;
                odd.next = current;
                odd = odd.next;                
            }
            
            p = next;
            i++;
        }
        
        return head;
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Odd Even Linked List.
Memory Usage: 39.5 MB, less than 5.00% of Java online submissions for Odd Even Linked List.
```



## Solution 2 双指针

通过双指针移动确实非常巧秒, 但没有上面的高效, 因为可以发现上面的只有一半的元素进行了交换. 

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode oddEvenList(ListNode head) {                
        if (head == null || head.next == null) {
            return head;
        }
        
        ListNode odd = head;
        ListNode even = head.next;
        ListNode evenHead = even;
        
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }
        
        odd.next = evenHead;
        
        return head;
    }
}
```

执行结果

```java
Runtime: 1 ms, faster than 8.36% of Java online submissions for Odd Even Linked List.
Memory Usage: 42.1 MB, less than 5.00% of Java online submissions for Odd Even Linked List.
```