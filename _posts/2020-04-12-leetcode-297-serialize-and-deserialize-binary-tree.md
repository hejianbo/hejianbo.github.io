---
layout: post
title: 每日一题 - LeetCode 297.Serialize and Deserialize Binary Tree
tags:
- leetcode
- 树
categories: leetcode
description: LeetCode 297.Serialize and Deserialize Binary Tree
---

# 297.Serialize and Deserialize Binary Tree


## Description

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

**Example:** 

```
You may serialize the following tree:

    1
   / \
  2   3
     / \
    4   5

as "[1,2,3,null,null,4,5]"
```

**Clarification:** The above format is the same as [how LeetCode serializes a binary tree](https://leetcode.com/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

**Note:** Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.



## Solution 1 错误解法

这应该是最普通的解法, 教材上也都讲过这种, 这里根据**先序遍历**与**中序遍历**来推出结果.

这个解错误的原因是这个题只是一棵二叉树，而不是一棵二叉搜索树. 所以不满足二叉搜索树的性质, 通过这种方式就会出现问题

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        // 先序遍历
        sb.append("[");
        this.preOrder(root, sb);
        sb.append("]");
        // 分隔
        sb.append("|");
        // 中序遍历
        sb.append("[");
        this.inOrder(root, sb);
        sb.append("]");

        return sb.toString();
    }

    private void preOrder(TreeNode node, StringBuilder sb) {
        if (node == null) {
            return;
        }

        sb.append(",").append(node.val);
        this.preOrder(node.left, sb);
        this.preOrder(node.right, sb);
    }

    private void inOrder(TreeNode node, StringBuilder sb) {
        if (node == null) {
            return;
        }
        this.inOrder(node.left, sb);
        sb.append(",").append(node.val);
        this.inOrder(node.right, sb);
    }


    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data == null || data == "") {
            return null;
        }
        String[] items = data.split("\\|");
        // 先序遍历结果
        String preOrderString = items[0];
        String[] preOrders = preOrderString.substring(2, preOrderString.length() - 1).split(",");
        // 中序遍历结果
        String inOrderString = items[1];
        String[] inOrders = inOrderString.substring(2, inOrderString.length() - 1).split(",");

        return this.toTree(preOrders, inOrders, 0, preOrders.length, 0);
    }

    private TreeNode toTree(String[] preOrders, String[] inOrders, int preOrderStart, int preOrderEnd, int inOrderStart) {
        if (preOrderStart > preOrderEnd) {
            return null;
        }
        // 根结点
        String rootStr = preOrders[preOrderStart];
        // 根在中序遍历中的位置
        int inOrderRoot = -1;
        for (int i = inOrderStart; i < inOrders.length; i++) {
            // 找到中序遍历中根的位置
            if (preOrders[preOrderStart].equals(inOrders[i])) {
                inOrderRoot = i;
                break;
            }
        }
        // 如果有右孩子,则获取右孩子
        int preOrderRightStart = preOrderStart + inOrderRoot - inOrderStart + 1;

        TreeNode root = new TreeNode(Integer.valueOf(rootStr));
        // 如果有左孩子获取左孩子, 如果根比inOrderStart大, 则表示有左孩子
        if (inOrderStart < inOrderRoot) {
            root.left = this.toTree(preOrders, inOrders, preOrderStart + 1, preOrderRightStart - 1, inOrderStart);
        }

        if (preOrderRightStart < preOrders.length) {
            TreeNode right = this.toTree(preOrders, inOrders, preOrderRightStart, preOrderEnd, inOrderRoot + 1);
            root.right = right;
        }

        return root;
    }
}
```



## Solution 2 基于先序遍历

同理也可以基于遍历方式

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
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) {
            return "";
        }
        // 先序
        StringBuilder sb = new StringBuilder();
        this.preOrder(root, sb);
        return sb.substring(1, sb.length());
    }
    
    private void preOrder(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append(",null");
            return;
        }
        sb.append(",").append(node.val);
        this.preOrder(node.left, sb);
        this.preOrder(node.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data == null || data == "") {
            return null;
        }
        String[] items = data.split(",");
        
        return this.toTree(items, 0).node;
    }
    
    private TreeNodeHelper toTree(String[] items, int index) {
        if (index >= items.length || items[index].equals("null")) {
            return new TreeNodeHelper(null, index);
        }
        TreeNode root = new TreeNode(Integer.valueOf(items[index]));
        TreeNodeHelper leftNodeHelper = this.toTree(items, index + 1);
        root.left = leftNodeHelper.node;
        TreeNodeHelper rightNodeHelper = this.toTree(items, leftNodeHelper.index + 1);
        root.right = rightNodeHelper.node;
        return new TreeNodeHelper(root, rightNodeHelper.index);
    }
    
    static class TreeNodeHelper {
        TreeNode node;
        int index;
        TreeNodeHelper(TreeNode node, int index) {
            this.node = node;
            this.index = index;
        }
    }
}
```

执行结果

```
Runtime: 13 ms, faster than 52.66% of Java online submissions for Serialize and Deserialize Binary Tree.
Memory Usage: 49.1 MB, less than 5.71% of Java online submissions for Serialize and Deserialize Binary Tree.
```