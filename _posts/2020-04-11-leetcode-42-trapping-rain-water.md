---
layout: post
title: 每日一题 - LeetCode 42. Trapping Rain Water
tags:
- leetcode
- 动态规划
- Two Pointers
categories: leetcode
description: LeetCode 42. Trapping Rain Water
---

# 42. Trapping Rain Water

## Description

Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)
The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. **Thanks Marcos** for contributing this image!

**Example:**

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```



## Solution 1

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        if (height == null || n == 0) {
            return 0;
        }
        
        int waters = 0;
        int preHigherIndex = 0;                
        
        // 水的数量 = 上一个最高到当前位置i前一个位置的体积 - 不是水的数量
        for (int i = 1;i < n; i++) {
            if (height[i] >= height[preHigherIndex]) {
                waters += height[preHigherIndex] * (i - preHigherIndex ) -  this.getNoneWaters(height, preHigherIndex, i - 1);
                preHigherIndex = i;
            }
        }
        
        // 上面因为要找到一个更高的非水位的位置, 所以会有剩余， 这个循环倒过再算一次就可以了
        if (preHigherIndex < n - 1) {
            int end = preHigherIndex;
            preHigherIndex = n - 1;
            for (int i = n - 2; i >= end; i--) {
                if (height[i] >= height[preHigherIndex]) {
                    waters += height[preHigherIndex] * (preHigherIndex - i) -  this.getNoneWaters(height, i + 1, preHigherIndex);
                    preHigherIndex = i;
                }
            }
        }
        
        return waters;
    }
    
    private int getNoneWaters(int[] height, int start, int end) {
        int sum = 0;
        for (int i = start; i<= end; i++) {
            sum += height[i];
        }
        return sum;
    }
}
```

运行结果

```
Runtime: 1 ms, faster than 92.65% of Java online submissions for Trapping Rain Water.
Memory Usage: 39.3 MB, less than 26.71% of Java online submissions for Trapping Rain Water.
```



## Solution 2 暴力

暴力算法主要是引出后面其它的算法。

```java
class Solution {    
    public int trap(int[] height) {
        int n = height.length;
        if (height == null || n == 0) {
            return 0;
        }
        
        int waters = 0;
        for (int i = 1; i < n -1; i++) {
            // 当前位置左边最大值
            int leftMax = height[i - 1];
            for (int left = i - 2; left >= 0; left--) {   
                leftMax = Math.max(leftMax, height[left]);
            }
            // 当前位置右边最大值
            int rightMax = height[i + 1];
            for (int right = i + 2; right < n; right++) {
                rightMax = Math.max(rightMax, height[right]);
            }
            // 左右最大值最小值
            int minMax = Math.min(leftMax, rightMax);
            // 如果当前位置的高度小于上面左右两边小的元素, 则说明可以积水
            waters += height[i] < minMax ? minMax - height[i] : 0;
        }
        
        return waters;
    }
}
```



## Solution 3 动态规划

观察上面暴力算法, 可以发现一个很大的问题, 就是每计算一个位置都会重复的去找左边与右边的最大值, 所以这时候我们就会想到，能否**消除这个重复**，这就是下面**动态规划**算法要干的事情。

> 个人认为从暴力进化下去的算法应该算是常规算法，否则一般应该算是巧算，对于我个人来说，常规算法能解大多数题，巧算靠当时的灵感。

```java
class Solution {    
    public int trap(int[] height) {
        int n = height.length;
        if (height == null || n == 0) {
            return 0;
        }
        
        // 左边最大值
        int[] maxLefts = new int[n];
        maxLefts[0] = height[0];
        for (int i = 1; i < n; i++) {
            maxLefts[i] = Math.max(maxLefts[i - 1], height[i]);
        }
        // 右边最大值
        int[] maxRights = new int[n];
        maxRights[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            maxRights[i] = Math.max(maxRights[i + 1], height[i]);
        }
        
        int waters = 0;
        for (int i = 1; i < n -1; i++) {
            int leftMax = maxLefts[i];            
            int rightMax = maxRights[i];
            int minMax = Math.min(leftMax, rightMax);            
            waters += height[i] < minMax ? minMax - height[i] : 0;
        }
        
        return waters;
    }
}
```

运行结果

```
Runtime: 1 ms, faster than 92.65% of Java online submissions for Trapping Rain Water.
Memory Usage: 39 MB, less than 37.67% of Java online submissions for Trapping Rain Water.
```



## Solution 4 Stack

这个解法其实与第1个的解法思想很接近

```java
class Solution {    
    public int trap(int[] height) {
        int answer = 0;
        int n = height.length;
        if (height == null || n == 0) {
            return answer;
        }
        
        Stack<Integer> stack = new Stack<Integer>();        
        int index = 0;
        while (index < n) {
            while(!stack.isEmpty() && height[index] > height[stack.peek()]) {
                int popIndex = stack.pop();
                // 如果栈中没有元素了, 则形成不了槽， 装不了水
                if (stack.isEmpty()) {
                    break;
                }
                // 当前位置index与栈顶元素位置之间的空位数量, 因为在这区间已经计算过的数量不包含popIndex之上的值, 
                // 即前面每个计算过的值都要包含这部分
                int distance = index - 1 - stack.peek();
                int value = Math.min(height[index], height[stack.peek()]) - height[popIndex];
                answer += distance * value;
            }
            
            stack.push(index++);
        }
        
        return answer;
    }
}
```

计算结果

```
Runtime: 2 ms, faster than 23.65% of Java online submissions for Trapping Rain Water.
Memory Usage: 38.9 MB, less than 47.26% of Java online submissions for Trapping Rain Water.
```

## Solution 5 Two Pointers

这个解法其实对解法一的优化

```java
class Solution {    
    public int trap(int[] height) {
        int answer = 0;
        int n = height.length;
        if (height == null || n == 0) {
            return answer;
        }
        
        int left = 0;
        int leftMax = 0;
        int right = n - 1;
        int rightMax = 0;
        
        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] <= leftMax) {
                    answer += leftMax - height[left];
                } else {
                    leftMax = height[left];
                }
                left++;
                
            } else {
                if (height[right] <= rightMax) {
                    answer += rightMax - height[right];
                } else {
                    rightMax = height[right];
                }
                right--;
            }            
        }
        return answer;
    }
}
```

计算结果
```
Runtime: 1 ms, faster than 92.65% of Java online submissions for Trapping Rain Water.
Memory Usage: 39 MB, less than 37.67% of Java online submissions for Trapping Rain Water.
```
