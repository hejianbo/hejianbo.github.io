---
layout: post
title: 每日一题 - 73.Set Matrix Zeroes
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 73.Set Matrix Zeroes
---

# 73.Set Matrix Zeroes

https://leetcode.com/problems/set-matrix-zeroes/

## Description

Given a *m* x *n* matrix, if an element is 0, set its entire row and column to 0. Do it [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm).

**Example 1:**

```
Input: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
Output: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```

**Example 2:**

```
Input: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
Output: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

**Follow up:**

- A straight forward solution using O(*m**n*) space is probably a bad idea.
- A simple improvement uses O(*m* + *n*) space, but still not the best solution.
- Could you devise a constant space solution?



## Solution 1

首先写出直观的解法. 空间复杂度为O(m+n).

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {
            return;
        }
        
        int rows = matrix.length;
        int columns = matrix[0].length;
        
        // 保存需要置为零的行与列
        Set<Integer> zeroRows = new HashSet<>();
        Set<Integer> zeroColumns = new HashSet<>();
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == 0) {
                    zeroRows.add(i);
                    zeroColumns.add(j);
                }
            }
        }
        
        // 行置为零
        for (Integer row : zeroRows) {
            for (int j = 0; j < columns; j++) {
                matrix[row][j] = 0;
            }
        }
        // 列置为零
        for (Integer column : zeroColumns) {
            for (int i = 0; i < rows; i++) {
                matrix[i][column] = 0;
            }
        }
    }
}
```

执行结果

```
Runtime: 2 ms, faster than 30.67% of Java online submissions for Set Matrix Zeroes.
Memory Usage: 41.1 MB, less than 98.57% of Java online submissions for Set Matrix Zeroes.
```



## Solution 2

这个解是O(1)的空间复杂度. 即借助了行首与列首的值来辅助标记该行与该列是否需要被置为0.

> 另外, leetcode另一个解, 看到引入了哨兵值（-1000000）来标识是否需要被修改为零, 虽然答案是正确的, 但感觉应该是有问题的, 因为这个解没有给出矩阵每个值的取值范围， 如果这个位置刚好是这个哨兵值, 感觉应该是过不了的

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {
            return;
        }
        
        int rows = matrix.length;
        int columns = matrix[0].length;
        
        boolean firstRow = false;
        boolean firstColumn = false;
        
        for (int i = 0; i < rows; i++) {
            // 第一列的元素出现0, 则第一列需要全部标置为0
            if (matrix[i][0] == 0) {
                firstColumn = true;
            }
            for (int j = 0; j < columns; j++) { 
                // 第一行的元素出现零, 则第一行的元素需要被标识为零
                if (i == 0 && matrix[i][j] == 0) {
                    firstRow = true;
                }
                // 将行首与列首置为0, 因为行首与列首已经访问过了, 所以被标记为零是不会影响的
                if (matrix[i][j] == 0) {   
                    // 第i行需要设置为0
                    matrix[i][0] = 0;
                    // 第j列需要设置为0
                    matrix[0][j] = 0;
                }
            }
        }
        
        // 检测列首是否为零, 如果为零, 则将该列置为零
        for (int j = 1; j < columns; j++) {
            if (matrix[0][j] == 0) {
                for (int i = 0; i < rows; i++) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // 检测行首是否为零, 如果为零, 则将该行置为零
        for (int i = 1; i < rows; i++) {
            if (matrix[i][0] == 0) {
                for (int j = 0; j < columns; j++) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // 第一列, 因为刚才没有处理第一列
        if (firstColumn) {            
            for (int i = 0; i < rows; i++) {
                matrix[i][0] = 0;
            }            
        }
        
        // 第一行, 因为刚才没有处理第一行
        if (firstRow) {            
            for (int i = 0; i < columns; i++) {
                matrix[0][i] = 0;
            }            
        }
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 93.07% of Java online submissions for Set Matrix Zeroes.
Memory Usage: 40.4 MB, less than 100.00% of Java online submissions for Set Matrix Zeroes.
```

