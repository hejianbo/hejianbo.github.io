---
layout: post
title: 每日一题 - 91. Decode Ways
tags:
- leetcode
- Medium
- 动态规划
categories: leetcode
description: LeetCode 91. Decode Ways
---

# 91. Decode Ways

https://leetcode.com/problems/decode-ways/

## Description

A message containing letters from `A-Z` is being encoded to numbers using the following mapping:

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

Given a **non-empty** string containing only digits, determine the total number of ways to decode it.

**Example 1:**

```
Input: "12"
Output: 2
Explanation: It could be decoded as "AB" (1 2) or "L" (12).
```

**Example 2:**

```
Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```

## Solution 1. 穷举 [Time Limit Exceeded]

```java
class Solution {
    private int count = 0;
    
    public int numDecodings(String s) {
        if (s == null || s.isEmpty()) {
            return 0;
        }
        
        this.helper(s, 0);
        
        return this.count;
    }
    
    private void helper(String s, int start) {
        if (start == s.length()) {
            this.count++;
            return;
        }
        if (s.charAt(start) != '0') {
            // 1个元素的情况
            this.helper(s, start + 1);
            // 2个元素的情况
            if (start + 1 < s.length() && Integer.valueOf(s.substring(start, start + 2)) <= 26) {
                this.helper(s, start + 2);
            }        
        }
    }
}
```



## Solution 2

注解观察规律

```
2261
2 		- 1 (2)
22  	- 2 (2, 2) (22)
226 	- 3 (2, 26) (2, 2, 6) (22, 6)
2261	- 3 (2, 26, 1) (2, 2, 6, 1) (22, 6, 1)
```

从上面我们可以可以得到递推式 

- dp[i] = dp[i - 2] + dp[i - 1] 
- dp[i - 2] : 表示的是以i结尾的两位数, 所以必须要是正确的值, 即[10, 26]
- dp[i - 1]: 表示的是以i结尾的1位数, 所以必须在[1, 9]



```java
class Solution {
    
    public int numDecodings(String s) {
        if (s == null || s.isEmpty()) {
            return 0;
        }
        int n = s.length();
        int[] dp = new int[n + 1];
        // 初始条件
        dp[0] = s.charAt(0) != '0' ? 1 : 0;
        dp[1] = dp[0];
        
        // 为了方便计算, dp的索引从1开始, 所以每个字符在dp中的索引位置需要 + 1
        for (int i = 1; i < n; i++) {
            // 如果是合法的1位数
            if (s.charAt(i) != '0') {
                dp[i + 1] = dp[i];
            }
            // 如果是合法的两位数
            int value = Integer.valueOf(s.substring(i - 1, i + 1));
            if (value >= 10 && value <= 26) {
                dp[i + 1] += dp[i - 1];
            }
        }
        
        
        return dp[n];
    }
    
   
}
```

执行结果

```
Runtime: 2 ms, faster than 64.04% of Java online submissions for Decode Ways.
Memory Usage: 38.3 MB, less than 5.66% of Java online submissions for Decode Ways.
```

