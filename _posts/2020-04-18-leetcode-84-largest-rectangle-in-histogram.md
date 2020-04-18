---
layout: post
title: 每日一题 - LeetCode 84.Largest Rectangle in Histogram
tags:
- leetcode
- 分治
categories: leetcode
description: LeetCode 84.Largest Rectangle in Histogram
---

# 84.Largest Rectangle in Histogram

https://leetcode.com/problems/largest-rectangle-in-histogram/



## Description

Given *n* non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

 

![img](https://assets.leetcode.com/uploads/2018/10/12/histogram.png)
Above is a histogram where width of each bar is 1, given height = `[2,1,5,6,2,3]`.

 

![img](https://assets.leetcode.com/uploads/2018/10/12/histogram_area.png)
The largest rectangle is shown in the shaded area, which has area = `10` unit.

 

**Example:**

```
Input: [2,1,5,6,2,3]
Output: 10
```



## Solution 栈

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        
        int n = heights.length;
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int i = 0;
        int max = 0;
        while (i < n) {
            // 如果栈中不是递增的, 则进行处理
            while (stack.peek() != -1 && heights[stack.peek()] >= heights[i]) {
                // i - stack.peek() - 1 表示宽度
                max = Math.max(max, heights[stack.pop()] * (i - stack.peek() - 1));
            }
            stack.push(i++);            
        }
        
        // 剩余下的元素在在栈中是递增的
        while (stack.peek() != -1) {
            max = Math.max(max, heights[stack.pop()] * (n - stack.peek() - 1));
        }
        
        return max;
    }
}
```

执行结果

```java
Runtime: 8 ms, faster than 60.06% of Java online submissions for Largest Rectangle in Histogram.
Memory Usage: 40.7 MB, less than 81.82% of Java online submissions for Largest Rectangle in Histogram.
```



## Solution 2 分治

这题其实通过分治的方式来做，其实更简单, 并且更容易理解.

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        
        return this.divideAndConquer(heights, 0, heights.length - 1);
    }
    
    private int divideAndConquer(int[] heights, int left, int right) {
        if (left == right) {
            return heights[left];
        }
        int middle = left + (right - left) / 2;
        // 左边最大值
        int leftMax = this.divideAndConquer(heights, left, middle);
        // 右边最大值
        int rightMax = this.divideAndConquer(heights, middle + 1, right);
        
        int mergedMax = this.merge(heights, left, middle, right);
        
        return Math.max( Math.max(leftMax, rightMax), mergedMax);
    }
    
    private int merge(int[] heights, int left, int middle, int right) {
        int max = 0;
        
        int i = middle;
        int j = middle + 1;
        
        int min = Math.min(heights[i], heights[j]);
        while (i >= left && j <= right) {   
            // 合并时当前最大值
            max = Math.max(max, min * (j - i + 1));
            // 结束
            if (i == left && j == right) {
                break;  
            } else if (i == left) {
                j++;                
                min = Math.min(min, heights[j]);
            } else if (j == right) {
                i--;
                min = Math.min(min, heights[i]);
            } else if (heights[i - 1] > heights[j + 1]) {
                i--;
                min = Math.min(min, heights[i]);
            } else {
                j++;
                min = Math.min(min, heights[j]);
            }
        }
        
        return max;
    }
}
```

执行结果

```
Runtime: 2 ms, faster than 95.63% of Java online submissions for Largest Rectangle in Histogram.
Memory Usage: 40.7 MB, less than 81.82% of Java online submissions for Largest Rectangle in Histogram.
```

