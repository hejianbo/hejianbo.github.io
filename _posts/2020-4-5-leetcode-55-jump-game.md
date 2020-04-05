---
layout: post
title: 每日一题: LeetCode 55.Jump Game
tags:
- leetcode
- 动态规划
- 回溯
- 贪婪
categories: leetcode
description: LeetCode 55.Jump Game
---

## leet code 55. Jump Game

## 问题描述

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```

## Solution 1 回溯法

此方法会**超时**

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return true;
        }
        
        return this.backtracking(nums, 0);
    }
    
    private boolean backtracking(int[] nums, int start) {
        if (start >= nums.length - 1) {
            return true;
        }
        for (int i = nums[start]; i >= 1; i--) {
            if (this.backtracking(nums, start + i)) {
                return true;
            }            
        }
        return false;
    }
}
```

上面回溯的时候可能会出现很多重复计算, 所以可以加一个cache消除这种重复

消除重复后, 效率也不高, 运行结果如下

```
Runtime: 2184 ms, faster than 5.03% of Java online submissions for Jump Game.

Memory Usage: 46.7 MB, less than 5.13% of Java online submissions for Jump Game.

Next challenges:

```

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return true;
        }
        // 表示如果在某一步，无论如何跳都会失败
        int[] flags = new int[nums.length];
        
        return this.backtracking(nums, 0, flags);
    }
    
    private boolean backtracking(int[] nums, int start, int[] flags) {
        if (start >= nums.length - 1) {
            return true;
        }
        if (flags[start] == 1) {
            return false;
        }
        for (int i = nums[start]; i >= 1; i--) {
            if (this.backtracking(nums, start + i, flags)) {
                return true;
            }            
        }
        flags[start] = 1;
        return false;
    }
}
```

## Solution 2 动态规划

动态规划主要是为了消除递归.

```
Runtime: 951 ms, faster than 6.99% of Java online submissions for Jump Game.
Memory Usage: 44.3 MB, less than 6.84% of Java online submissions for Jump Game.

```


```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return true;
        }
        int n = nums.length;
        // cache
        int[] memos = new int[n];        
        memos[n - 1] = 1;
        
        for (int i = n - 2; i >= 0; i--) {
            // can jump longest position
            int longestPosition = Math.min(i + nums[i], n - 1);
            for (int j = longestPosition; j >= 1; j--) {
                // has successor can reach destination
                if (memos[j] == 1) {
                    // so current position can reach destination
                    memos[i] = 1;
                    break;
                }
            }
        }
        return memos[0] == 1;
    }    
}
```







## Solution 3 贪婪

```
Runtime: 1 ms, faster than 98.44% of Java online submissions for Jump Game.
Memory Usage: 41.4 MB, less than 34.19% of Java online submissions for Jump Game.

```


```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return true;
        }
        int n = nums.length;
        // 初始状态下能跳到最远的地方
        int max = nums[0];
        int i = 0;
        while (i <= max && max <= n - 1) {
            max = Math.max(max, nums[i] + i);
            i++;
        }
        
        return max >= n - 1;
    }    
}
```
