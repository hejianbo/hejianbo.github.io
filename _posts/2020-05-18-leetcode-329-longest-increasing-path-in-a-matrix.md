---
layout: post
title: 每日一题 - 329.Longest Increasing Path in a Matrix
tags:
- leetcode
- Hard
- 动态规划
- 深度优化搜索
categories: leetcode
description: LeetCode 329.Longest Increasing Path in a Matrix
---

# 329.Longest Increasing Path in a Matrix

https://leetcode.com/problems/longest-increasing-path-in-a-matrix/

## Description

Given an integer matrix, find the length of the longest increasing path.

From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary (i.e. wrap-around is not allowed).

**Example 1:**

```
Input: nums = 
[
  [9,9,4],
  [6,6,8],
  [2,1,1]
] 
Output: 4 
Explanation: The longest increasing path is [1, 2, 6, 9].
```

**Example 2:**

```
Input: nums = 
[
  [3,4,5],
  [3,2,6],
  [2,2,1]
] 
Output: 4 
Explanation: The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
```

## Solution 1 深度优化搜索

这个是基于深度优化搜索的DP的递归解决方法.

```java
class Solution {
    private int max = 0;
    
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {
            return 0;
        }
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        // 用来表示是否已经被访问过
        int[][] cache = new int[rows][cols];
        // 初始化为 -1        
        for (int i = 0; i < rows; i++) {
            Arrays.fill(cache[i], -1);
        }        
        
        // 用循环是为了避免一次深度搜索可能访问不了所有的元素, 也有可能一次dfs就访问全的可能, 
        // 所以有一个cache, 同时也避免多次dfs同一个位置        
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                // 值为-1表示还没有访问过
                if (cache[row][col] == -1) {
                    this.dfs(matrix, cache, row, col);
                }
            }
        }
        
        return this.max;        
    }
    
    private int dfs(int[][] matrix, int[][] cache, int row, int col) {
        // 初始值全为1, 表示就当前结点[row][col]数量
        int top = 1;
        int left = 1;
        int right = 1;
        int bottom = 1;
        // 对四个方向分别运用深度优化搜索
        //　上
        if (row - 1 >= 0 && matrix[row][col] < matrix[row - 1][col]) {
            top = 1 + (cache[row - 1][col] >= 0 ? cache[row - 1][col] : this.dfs(matrix, cache, row - 1, col));
        }
        //　下
        if (row + 1 < matrix.length && matrix[row][col] < matrix[row + 1][col]) {
            bottom = 1 + (cache[row + 1][col] >= 0 ? cache[row + 1][col] : this.dfs(matrix, cache, row + 1, col));
        }
        //　左
        if (col - 1 >= 0 && matrix[row][col] < matrix[row][col - 1]) {
            left = 1 + (cache[row][col - 1] >= 0 ? cache[row][col - 1] : this.dfs(matrix, cache, row, col - 1));
        }        
        //　右        
        if (col + 1 < matrix[0].length && matrix[row][col] < matrix[row][col + 1]) {
            right = 1 + (cache[row][col + 1] >= 0 ? cache[row][col + 1] : this.dfs(matrix, cache, row, col + 1));
        }
        
        // 保存最大的值
        cache[row][col] = Math.max(Math.max(top, left), Math.max(right, bottom));      
        this.max = Math.max(this.max, cache[row][col]);
        
        return cache[row][col];
    }
}
```

执行结果

```
Runtime: 5 ms, faster than 100.00% of Java online submissions for Longest Increasing Path in a Matrix.
Memory Usage: 39.8 MB, less than 97.96% of Java online submissions for Longest Increasing Path in a Matrix.
```



## Solution 2. 先排序, 再用动态规划的递推

这个解是观察上面的解决发现的, 思路是这样的:

- 一般递推的动态规划如果化成递推的方案, 大多数情况会是一个更优的解
- 观察上面的执行结果, 因为我们要找一个升序的解, 所以突然想到如果我们直接就按元素的增序依次取出就可以了
- 每个元素的长度, 就看他四周有没有已经取出的最小元素, 如果没有长度就为1, 有就加上四周的最大值

得到的递推式和上面解法１是一样的, 但是很大的一个优化是, 如果四周没有访问过的不再进行dfs了, 所以效率一定会高很多．

```
dp[row][col] = Max{ 
					dp[row - 1][col], 
					dp[row + 1][col], 
					dp[row][col - 1], 
					dp[row][col + 1]
				};
```

```java
class Solution {
  
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {
            return 0;
        }
        int rows = matrix.length;
        int cols = matrix[0].length;        
        
        // 定义一个最小堆
        Queue<int[]> queue = new PriorityQueue <>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return Integer.compare(matrix[o1[0]][o1[1]], matrix[o2[0]][o2[1]]);
            }
        });
        
        // 将所有元素放入堆中
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                queue.offer(new int[] { row, col });
            }
        }
        
        int max = 0;
        // 缓存, 行列都放大2, 避免计算边界麻烦
        int[][] cache = new int[rows][cols];
        while (!queue.isEmpty()) {                       
            int[] item = queue.poll();
            int row = item[0];
            int col = item[1];
            
            // 因为有相等的元素存在, 所以还要比较, 如果没有相等元素, 可以直接通过cache得到结果
            
            int top = 1;
            int left = 1;
            int right = 1;
            int bottom = 1;     
            // 注意与上面的区别, 这里比较是大于号, 因为已经是升序的
            //　上
            if (row - 1 >= 0 && matrix[row][col] > matrix[row - 1][col]) {
                top = 1 + cache[row - 1][col];
            }
            //　下
            if (row + 1 < rows && matrix[row][col] > matrix[row + 1][col]) {
                bottom = 1 + cache[row + 1][col];
            }
            //　左
            if (col - 1 >= 0 && matrix[row][col] > matrix[row][col - 1]) {
                left = 1 + cache[row][col - 1];
            }        
            //　右        
            if (col + 1 < cols && matrix[row][col] > matrix[row][col + 1]) {
                right = 1 + cache[row][col + 1];
            }
            
            
            cache[row][col] = Math.max(Math.max(top, left), Math.max(bottom, right));            
            max = Math.max(cache[row][col], max);
        }
        
        return max;        
    }

}
```

执行结果

```
Runtime: 92 ms, faster than 6.60% of Java online submissions for Longest Increasing Path in a Matrix.
Memory Usage: 52.3 MB, less than 6.12% of Java online submissions for Longest Increasing Path in a Matrix.
```

> 实际执行结果, 效率更差了.............