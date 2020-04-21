---
layout: post
title: 每日一题 - 45. Jump Game II
tags:
- leetcode
- 动态规划
- 贪婪法
categories: leetcode
description: LeetCode 45. Jump Game II
---

# 45. Jump Game II

https://leetcode.com/problems/jump-game-ii/

## Description

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**Example:**

```
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

 

## Solution 1.贪婪法 

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        // 当前所处的位置
        int i = 0;
        // 最小跳数
        int minJumps = 0;
        while (i < n - 1) {
            // 如果在当前i上, 再跳一次就可以结束, 则终止循环
            if (i + nums[i] >= n - 1) {
                minJumps++;
                break;
            }
            // 在当前i上, 能跳出的最大步数
            int maxJump = nums[i];    
            // 下一跳的位置（即下一次迭代时的位置）
            // 下一跳取的位置, 必须是在当前能的所有位置上, 再跳出云能达到的最大postion的位置
            int next = 0;
            // 下一跳能跳出的最大postion
            int nextJumpMaxPosition = 0;
            for (int j = 1; j <= maxJump; j++) {
                // 下一跳跳出的postion = 当前跳出的位置 + 下一跳跳出的步数
                int nextJumpPosition = i + j + nums[i + j];
                if (nextJumpPosition > nextJumpMaxPosition) {
                    next = i + j;
                    nextJumpMaxPosition = nextJumpPosition;
                }
            }
            i = next;
            // 步数加1
            minJumps++;
        }
        
        return minJumps;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Jump Game II.
Memory Usage: 41.5 MB, less than 19.23% of Java online submissions for Jump Game II.
```

## Solution 2.动态规划


```java
class Solution {
    public int jump(int[] nums) {        
        int n = nums.length;        
        int[] dp = new int[n];        
        dp[n - 1] = 0;        
        
        for (int i = n - 2; i >= 0; i--) {
            if (i + nums[i] >= n - 1) {
                dp[i] = 1;
            } else {
                if (nums[i] <= 0) {
                    dp[i] = Integer.MAX_VALUE;
                } else {
                    // 转移函数: dp[i] = 1 + min{dp[i+1...i+nums[i]]}
                    int minJump = Integer.MAX_VALUE;
                    for (int j = i + 1; j <= i + nums[i]; j++) {
                        minJump = Math.min(minJump, dp[j]);
                    }
                    dp[i] = (minJump == Integer.MAX_VALUE ? Integer.MAX_VALUE : 1 + minJump);
                }
            }
        }
        
        return dp[0];
    }
}
```

执行结果

```
Runtime: 281 ms, faster than 26.19% of Java online submissions for Jump Game II.
Memory Usage: 41.5 MB, less than 13.46% of Java online submissions for Jump Game II.
```

