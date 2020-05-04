---
layout: post
title: 每日一题 - 116.Populating Next Right Pointers in Each Node
tags:
- leetcode
- 广度优化搜索
- 双队列
- Medium
categories: leetcode
description: LeetCode 116.Populating Next Right Pointers in Each Node
---

# 116.Populating Next Right Pointers in Each Node

https://leetcode.com/problems/populating-next-right-pointers-in-each-node/

这题对于熟悉树这种数据结构的人来说, 应该属于easy, 肯定达不到Medium的.

## Description

You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

 

**Follow up:**

- You may only use constant extra space.
- Recursive approach is fine, you may assume implicit stack space does not count as extra space for this problem.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

 

**Constraints:**

- The number of nodes in the given tree is less than `4096`.
- `-1000 <= node.val <= 1000`

## Solution 双队列

这道题显示很简单, 只需要通过按树的层级遍历(**广度优化搜索**), 然后在每层的时候设置next指针就行了.  

1. 这里通过**双队列**的式来实现, 前面也已经用过这种方法 [103.Binary Tree Zigzag Level Order Traversal](https://jianbo.me/leetcode/2020/05/03/leetcode-103-binary-tree-zigzag-level-order-traversal/#103binary-tree-zigzag-level-order-traversal),  单队列也是可以的，但我觉得单队列的代码没有双队列来的简单
2. 另外因为是满二叉树, 还可以通过将所有结点塞入数组，根据下标来直接计算也是可以的. 

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node next;

    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
};
*/

class Solution {
    public Node connect(Node root) {
        if (root == null) {
            return root;
        }
        
        Queue<Node> firstQueue = new LinkedList<>();
        Queue<Node> secondQueue = new LinkedList<>();
        firstQueue.offer(root);
        
        while (!firstQueue.isEmpty() || !secondQueue.isEmpty()) {
            if (!firstQueue.isEmpty()) {
                this.helper(firstQueue, secondQueue);
            } else {
                this.helper(secondQueue, firstQueue);
            }
        }
        
        return root;
    }   
    private void helper(Queue<Node> source, Queue<Node> target) {
        while (!source.isEmpty()) {
            Node node = source.poll();
            if (source.peek() != null) {
                node.next = source.peek();
            }
            if (node.left != null) {
                target.offer(node.left);
            }
            if (node.right != null) {
                target.offer(node.right);
            }
        }
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 50.99% of Java online submissions for Populating Next Right Pointers in Each Node.
Memory Usage: 40.2 MB, less than 6.35% of Java online submissions for Populating Next Right Pointers in Each Node.
```

