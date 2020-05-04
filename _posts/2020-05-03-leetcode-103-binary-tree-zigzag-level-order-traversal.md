---
layout: post
title: 每日一题 - 103.Binary Tree Zigzag Level Order Traversal
tags:
- leetcode
- 广度优化搜索
- 双队列
- Medium
categories: leetcode
description: LeetCode 103.Binary Tree Zigzag Level Order Traversal
---

# 103.Binary Tree Zigzag Level Order Traversal

https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/

此题主要用到了树的广度优化搜索遍历, 还是相当简单的.

## Description

Given a binary tree, return the *zigzag level order* traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).

For example:
Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

return its zigzag level order traversal as:

```
[
  [3],
  [20,9],
  [15,7]
]
```



## Solution 队列

这个解应该只要了解树以及使用队列进行广度搜索遍历, 应该是很基础的一道题.

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {        
        // 输出结果
        List<List<Integer>> answer = new ArrayList<>();
        if (root == null) {
            return answer;
        }
        
        // 为了识别到每个TreeNode属于那一层, 所以引入一个辅助变量来存储
        Map<TreeNode, Integer> nodeLevelMap = new HashMap<>();
        // 通过队列实现广度优化遍历
        Queue<TreeNode> queue = new LinkedList<>();
        // 先入队根结点        
        queue.offer(root);
        nodeLevelMap.put(root, 1);
        
        // 用来保存每一层的结果
        int level = 0;
        List<Integer> list = new ArrayList<>();
        while (!queue.isEmpty()) {               
            TreeNode node = queue.poll();
            // 获取当前结点所属层级, 
            // 如果比level大, 则表示进入了新的层级, 需要保存前面的数据到answer中
            int nodeLevel = nodeLevelMap.get(node);
            if (nodeLevel > level) {
                if (level != 0) {
                    this.addToAnswer(answer, list, level);
                }
                level += 1;
            }            
            // 将node的两个孩子入队, 表示下一层级
            if (node.left != null) {
                queue.offer(node.left);
                nodeLevelMap.put(node.left, nodeLevel + 1);
            }
            if (node.right != null) {
                queue.offer(node.right);
                nodeLevelMap.put(node.right, nodeLevel + 1);
            }
            // 出队后保存值
            list.add(node.val);
        }
        // 保存最后一层的结果
        if (list.size() > 0) {
            this.addToAnswer(answer, list, level);
        }        
        
        return answer;
    }
    
    private void addToAnswer(List<List<Integer>> answer, List<Integer> list, int level) {
        List<Integer> temp = new ArrayList<>(list);
        list.clear();
        if (level % 2 == 0) {
            Collections.reverse(temp);                        
        }
        answer.add(temp);
    }
}
```

执行结果

```
Runtime: 3 ms, faster than 9.44% of Java online submissions for Binary Tree Zigzag Level Order Traversal.
Memory Usage: 39.8 MB, less than 5.77% of Java online submissions for Binary Tree Zigzag Level Order Traversal.
```



## Solution 2 双队列

对上面进行优化,  突然想到的这个解, 好像印象中以前有类似的题用过双队列. 这个解主要做了这些优化:

1. 消掉了Map, 用第二个队列来代替, 这样只需要缓存一层的数据, 在数据量不大的情况下, 没啥区别
2. 上面的方法有一个List的copy, 这里也消掉了.
3. 同时借助双队列, 消掉了level级别这个变量

> 总之, 这个结果从空间与时间上来说, 几乎没啥变化, 但是代码更漂亮了.

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {        
        // 输出结果
        List<List<Integer>> answer = new ArrayList<>();
        if (root == null) {
            return answer;
        }
        
        // 通过队列实现广度优化遍历
        LinkedList<TreeNode> firstQueue = new LinkedList<>();
        LinkedList<TreeNode> secondQueue = new LinkedList<>();
        // 先入队根结点        
        firstQueue.offer(root);
        
        while (!firstQueue.isEmpty() || !secondQueue.isEmpty()) {               
            List<Integer> list;
            if (!secondQueue.isEmpty()) {
                list = this.helper(secondQueue, firstQueue);                
                Collections.reverse(list);
            } else {
                list = this.helper(firstQueue, secondQueue);                
            }
            answer.add(list);
        }      
        
        return answer;
    }
    
    private List<Integer> helper(LinkedList<TreeNode> source, LinkedList<TreeNode> target) {
        List<Integer> list = new ArrayList<>();
        while (!source.isEmpty()) {
            TreeNode node = source.pollFirst();
            list.add(node.val);
            
            if (node.left != null) {
                target.offerLast(node.left);
            }
            if (node.right != null) {
                target.offerLast(node.right);
            }
        }
        return list;
            
    }
}
```

执行结果

```
Runtime: 3 ms, faster than 9.55% of Java online submissions for Binary Tree Zigzag Level Order Traversal.
Memory Usage: 40.1 MB, less than 5.77% of Java online submissions for Binary Tree Zigzag Level Order Traversal.
```

