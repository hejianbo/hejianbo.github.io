---
layout: post
title: 每日一题 - LeetCode 98. Validate Binary Search Tree
tags:
- leetcode
- 二叉树
categories: leetcode
description: LeetCode 98. Validate Binary Search Tree
---

# 98.Validate Binary Search Tree 


## Description

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

 

**Example 1:**

```
    2
   / \
  1   3

Input: [2,1,3]
Output: true
```

**Example 2:**

```
    5
   / \
  1   4
     / \
    3   6

Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```



## Solution 递归

此题看上去非常简单, 但实际做的时候容易犯错, 在进行树的结点进行检验时, 一般只会比较父子2个结点, 而实际只比较2个结点是不够的.

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
    public boolean isValidBST(TreeNode root) {
        return this.isValid(root, null, null);
    }
    
    private boolean isValid(TreeNode node, TreeNode lower, TreeNode upper) {
        if (node == null) {
            return true;
        }
		
        if (lower != null && node.val <= lower.val) {
            return false;
        }
        
        if (upper != null && node.val >= upper.val) {
            return false;
        }
        // 左孩子比当前结点小
        // lower2种情况: 
        // 1. 为null表示该node.left结点所在路径一直在左孩子
        // 2. 当不为null表示其node.left某个祖先在某个右子树上, 所以此时其值必须要大于该转折点的值
        if (!this.isValid(node.left, lower, node)) {
            return false;
        }
        // 右孩子比当前值大
        // 原理同上, upper也有2种情况
        // 1. 为null表示该node.right所在路径一直在右子树上
        // 2. 非null表示node.right某个祖先在左子树上, 所以其值需要小于该转折点的值
        return this.isValid(node.right, node, upper); 
    }
}
```

