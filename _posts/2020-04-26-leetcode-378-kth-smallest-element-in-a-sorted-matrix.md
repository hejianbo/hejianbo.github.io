---
layout: post
title: 每日一题 - 378.Kth Smallest Element in a Sorted Matrix
tags:
- leetcode
- 广度优化搜索
- 堆
categories: leetcode
description: LeetCode 378.Kth Smallest Element in a Sorted Matrix
---

# 378.Kth Smallest Element in a Sorted Matrix

https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/

## Description

Given a *n* x *n* matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

**Example:**

```
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

return 13.
```

**Note:** 
You may assume k is always valid, 1 ≤ k ≤ n2.



## Solution 1 最大堆

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int m = matrix[0].length;        
        // 最大堆
        Queue<Integer> queue = new PriorityQueue<>(Comparator.reverseOrder());                 
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                queue.offer(matrix[i][j]);
                if (++count > k) {
                    queue.poll();
                }
            }
        }
        
        
        return queue.peek();
    }
}
```

执行结果

```
Runtime: 51 ms, faster than 5.04% of Java online submissions for Kth Smallest Element in a Sorted Matrix.
Memory Usage: 55.2 MB, less than 5.41% of Java online submissions for Kth Smallest Element in a Sorted Matrix.
```



## Solution 2 广度优化搜索

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int m = matrix[0].length;        
        
        int[][] visited = new int[n][m];
        Queue<Node> queue = new PriorityQueue<>(Comparator.comparingInt(o -> o.val));                    
        queue.offer(new Node(0, 0, matrix[0][0]));        
        int count = 0;
        while (!queue.isEmpty()) {
            Node node = queue.poll();
            if (++count == k) {
                return node.val;
            }
            int i = node.i; 
            int j = node.j;
            if (i + 1 < n && visited[i + 1][j] == 0) {
                queue.offer(new Node(i + 1, j, matrix[i + 1][j]));      
                visited[i + 1][j] = 1;
            }
            if (j + 1 < m && visited[i][j + 1]  == 0) {
                queue.offer(new Node(i, j + 1, matrix[i][j + 1]));
                visited[i][j + 1] = 1;
            }
        }
        return 0;
    }
    
    
    static class Node {
        int i;
        int j;
        int val;
        Node(int i, int j, int val) {
            this.i = i;
            this.j = j;
            this.val = val;
        }
    }
}
```

执行结果

```
Runtime: 36 ms, faster than 5.99% of Java online submissions for Kth Smallest Element in a Sorted Matrix.
Memory Usage: 55.8 MB, less than 5.41% of Java online submissions for Kth Smallest Element in a Sorted Matrix.
```

