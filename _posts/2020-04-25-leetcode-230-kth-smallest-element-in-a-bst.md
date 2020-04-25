---
layout: post
title: 每日一题 - 230.Kth Smallest Element in a BST
tags:
- leetcode
- 二叉树
categories: leetcode
description: LeetCode 230.Kth Smallest Element in a BST
---

# 230.Kth Smallest Element in a BST

https://leetcode.com/problems/kth-smallest-element-in-a-bst/

## Description

Given a binary search tree, write a function `kthSmallest` to find the **k**th smallest element in it.

**Note:** 
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

**Example 1:**

```
Input: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
Output: 1
```

**Example 2:**

```
Input: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
Output: 3
```

**Follow up:**
What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?



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
    public int kthSmallest(TreeNode root, int k) {        
        Stack<TreeNode> stack = new Stack<>();
        TreeNode p = root;
        int i = 0;
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }
            if (!stack.isEmpty()) {
                TreeNode pop = stack.pop();                
                if (++i == k) {
                    return pop.val;
                }
                if (pop.right != null) {
                    p = pop.right;
                }
            }
        }
        
        return 0;
    }
}
```

执行结果
```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Kth Smallest Element in a BST.
Memory Usage: 39.8 MB, less than 5.51% of Java online submissions for Kth Smallest Element in a BST.
```

