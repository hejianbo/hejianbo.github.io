---
layout: post
title: 每日一题 - LeetCode 312.Burst Ballons
tags:
- leetcode
- 动态规划
categories: leetcode
description: LeetCode 312.Burst Ballons
---

# 312. Burst Balloons

## Description

Given `n` balloons, indexed from `0` to `n-1`. Each balloon is painted with a number on it represented by array `nums`. You are asked to burst all the balloons. If the you burst balloon `i` you will get `nums[left] * nums[i] * nums[right]` coins. Here `left` and `right` are adjacent indices of `i`. After the burst, the `left` and `right` then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.

**Note:**

- You may imagine `nums[-1] = nums[n] = 1`. They are not real therefore you can not burst them.
- 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

**Example:**

```
Input: [3,1,5,8]
Output: 167 
Explanation: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
             coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```



## Solution 1 动态规划自顶向下

这个解的关键是从最后点爆某个气球入手，这样就可以找到正确的子问题.

```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;         
        
        // 引入中间数组, 方便程序处理边界问题   
        int[] newNums = new int[n + 2];
        System.arraycopy(nums, 0, newNums, 1, n);        
        newNums[0] = 1;
        newNums[newNums.length - 1] = 1;
        
        int[][] dp = new int[n + 2][n + 2];
        
        return this.helper(newNums, 1, n, dp);
    }
    
    private int helper(int[] nums, int start, int end, int[][] dp) {        
        if (start > end) {
            return 0;
        }
        // 如果已经计算过, 则不需要计算
        if (dp[start][end] > 0) {
            return dp[start][end];
        }
        
        for (int i = start; i <= end; i++) {
            // 左边子问题
            int left = this.helper(nums, start, i - 1, dp);
            // 右边子问题
            int right = this.helper(nums, i + 1, end, dp);
            // 该区域最后一个被点爆的气球
            int current = nums[start - 1] * nums[i] * nums[end + 1];
            dp[start][end] = Math.max(dp[start][end], left + right + current);
        }
        
        return dp[start][end];
    }
}
```

执行结果

```
Runtime: 9 ms, faster than 33.27% of Java online submissions for Burst Balloons.
Memory Usage: 37.7 MB, less than 15.79% of Java online submissions for Burst Balloons.
```



## Solution 2 动态规划自底向上

对上面的解进行优化, 即消除重复.

上面的解虽然有cache，不需要重复计算，但还是有消耗的, 自底向下的动态规划就是消除这个重复的。

```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;         
        
        // 引入中间数组, 方便程序处理边界问题   
        int[] newNums = new int[n + 2];
        System.arraycopy(nums, 0, newNums, 1, n);        
        newNums[0] = 1;
        newNums[newNums.length - 1] = 1;
        
        int[][] dp = new int[n + 2][n + 2];
        
        
        for (int i = 1; i <= n; i++) {
            // dp[j][k]
            for (int j = 1; j <= n-i+1; j++) {
                int k = j + i - 1;
                for (int m = j; m <= k; m++) {
                    dp[j][k] = Math.max(dp[j][k], dp[j][m - 1] + dp[m + 1][k] + newNums[j - 1] * newNums[m] * newNums[k + 1]);
                }
            }
        }
        
        
        return dp[1][n];
    }   
    
}
```

执行结果

```java
Runtime: 7 ms, faster than 81.00% of Java online submissions for Burst Balloons.
Memory Usage: 37.8 MB, less than 10.53% of Java online submissions for Burst Balloons.

```



> 这个解是看的答案, 自底向上的这种思维还是得多练练, 不然稍微复杂一点的题就优化不出来