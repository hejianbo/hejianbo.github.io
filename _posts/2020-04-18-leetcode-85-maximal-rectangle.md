---
layout: post
title: 每日一题 - LeetCode 85.Maximal Rectangle
tags:
- leetcode
categories: leetcode
description: LeetCode 85.Maximal Rectangle
---

# 85.Maximal Rectangle

https://leetcode.com/problems/maximal-rectangle/

## Description

Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

**Example:**

```
Input:
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6

```

## Solution 1 暴力

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        int rows = matrix.length;
        int columns = matrix[0].length;
        
        int maxRectangle = 0;
        for (int n = 0; n < rows; n++) {
            for (int m = 0; m < columns; m++) {
                // 以[n][m]为起点的最大矩形面积
                int rectangle = 0;
                // 列
                for (int i = m; i < columns && matrix[n][i] == '1'; i++) {
                    // 行
                    for (int j = n; j < rows && matrix[j][i] =='1'; j++) {
                        // 以[j][i]结尾形成的矩形是否合法
                        boolean isValid = true; 
                        // 保持行不变, 比较对应位置列的元素
                        for (int a = i; a >= m; a--) {
                            if (matrix[j][a] == '0') {
                                isValid = false;
                                break;
                            }
                        }
                        if (isValid) {                            
                            rectangle = Math.max(rectangle, (j - n + 1) * (i - m + 1));                            
                        } else {
                            // 不合法则不需要继续扩展了
                            break;
                        }
                    }
                }
                maxRectangle = Math.max(maxRectangle, rectangle);
            }
        }
        
        return maxRectangle;
    }
}
```

执行结果

```
Runtime: 22 ms, faster than 7.34% of Java online submissions for Maximal Rectangle.
Memory Usage: 43.2 MB, less than 82.61% of Java online submissions for Maximal Rectangle.
```

## Solution 2 基于栈

可以基于84题的结果来计算. 略