---
layout: post
title: 每日一题 - 124.Binary Tree Maximum Path Sum 
tags:
- leetcode
categories: leetcode
description: LeetCode 124.Binary Tree Maximum Path Sum 
---

# 124.Binary Tree Maximum Path Sum 

https://leetcode.com/problems/binary-tree-maximum-path-sum/


## Description

Given a **non-empty** binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain **at least one node** and does not need to go through the root.

**Example 1:**

```
Input: [1,2,3]

       1
      / \
     2   3

Output: 6
```

**Example 2:**

```
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42
```



## Solution

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private int maxPathSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        this.helper(root);
        return this.maxPathSum;
    }
    
    private int helper(TreeNode node) {
        if (node.left == null && node.right == null) {
            this.maxPathSum = Math.max(node.val, this.maxPathSum);
            return node.val;
        } else if (node.left == null) {
            int right = helper(node.right);
            // 最大值在: 右孩子, 右孩子+根,根
            this.maxPathSum = Math.max(this.maxPathSum, Math.max(node.val, right + node.val));
            return Math.max(node.val, node.val + right);
        } else if (node.right == null) {
            int left = helper(node.left);
            // 最大值在: 左孩子, 左孩子 + 根, 根
            this.maxPathSum = Math.max(this.maxPathSum, Math.max(node.val, left + node.val));
            return Math.max(node.val, node.val + left);
        } else {
            int left = helper(node.left);
            int right = helper(node.right);
            // 最大值在: 左孩子，右孩子， 根，左孩子+根，右孩子+根, 左孩子+右孩子+根
            int maxChildAndRootOrRoot =Math.max(node.val, Math.max(left + node.val, right + node.val));
            int childrenAndRoot = left + right + node.val;
            this.maxPathSum = Math.max(this.maxPathSum, Math.max(maxChildAndRootOrRoot, childrenAndRoot));
            return maxChildAndRootOrRoot;
        }
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 22.94% of Java online submissions for Binary Tree Maximum Path Sum.
Memory Usage: 45.8 MB, less than 5.95% of Java online submissions for Binary Tree Maximum Path Sum.
```



看了下leetcode的答案,  推测出应该是可以不需要负数结点的, 相当于path长度为0就行了, 所以这程序就可以简单地处理了.

```java
class Solution {
    private int max = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        if (root == null) {
            return 0;
        }        
        this.helper(root);        
        return this.max;
    }
    
    private int helper(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int left = Math.max(this.helper(node.left), 0);
        int right = Math.max(this.helper(node.right), 0);
        
        this.max = Math.max(this.max, left + right + node.val);
        return node.val + Math.max(left, right);
    }
}
```

```
Runtime: 1 ms, faster than 22.94% of Java online submissions for Binary Tree Maximum Path Sum.
Memory Usage: 45.7 MB, less than 5.95% of Java online submissions for Binary Tree Maximum Path Sum.
```

