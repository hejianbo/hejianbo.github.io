---
layout: post
title: 每日一题 - 32.Longest Valid Parentheses
tags:
- leetcode
- 动态规划
categories: leetcode
description: LeetCode 32.Longest Valid Parentheses
---

# 32.Longest Valid Parentheses

https://leetcode.com/problems/longest-valid-parentheses/

## Description

Given a string containing just the characters `'('` and `')'`, find the length of the longest valid (well-formed) parentheses substring.

**Example 1:**

```
Input: "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()"
```

**Example 2:**

```
Input: ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()"
```



## Solution 1.暴力

直接暴力解法肯定是不行的，主要是通过暴力解法的不断优化来推出其它的解.

```java
class Solution {
    public int longestValidParentheses(String s) {
        int n = s.length();
             
        int longest = 0;
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (this.isValid(s, i, j)) {
                    longest = Math.max(longest, j - i + 1);
                }
            }
        }
        
        return longest;        
    }
    
    private boolean isValid(String s, int left, int right) {
        Stack<Integer> stack = new Stack<>();
        for (int i = left; i <= right; i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else if (stack.isEmpty()) {
                return false;
            } else {
                stack.pop();
            }
        }
        
        return stack.isEmpty();
    }
}
```

下面进行优化, 常规做法是首先第一步就是消除重复, 但执行结果还是超时, 因为很显示cahce永远没有命中的，所以完全是浪费. 代码如下:

```java
public int longestValidParentheses(String s) {
    int n = s.length();
    int[][] cache = new int[n][n];
    int longest = 0;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (cache[i][j] == 0) {
                cache[i][j] = this.isValid(s, i, j) ? 1 : 2;
            }
            if (cache[i][j] == 1) {
                longest = Math.max(longest, j - i + 1);
            }
        }
    }

    return longest;        
}
```

再次优化, 很明显, 如果已经知道某一个longest了, 比它小的范围肯定是没有要再计算了.

```java
public int longestValidParentheses(String s) {
    int n = s.length();        
    int longest = 0;
    for (int i = 0; i < n - 1; i++) {            
        for (int j = i +longest + 1; j < n; j++) {
            if (this.isValid(s, i, j)) {
                longest = Math.max(longest, j - i + 1);
            }
        }
    }
    return longest;        
}
```

上面还是会超时的, 所以我们就要寻求更多的优化条件.  

1. 通过观察发现正确的括号对必须左右括号数要相等才能进行匹配.
2. 最左边必须是**'('**，最右边必须是**')'**. 

所以根据上面的2个条件再一次进行优化.

```java
public int longestValidParentheses(String s) {
    int n = s.length();        
    int longest = 0;

    for (int left = 0; left < n; left++) {
        if (s.charAt(left) == '(') {
            int leftCount = 1;
            int rightCount = 0;
            for (int right = left + 1; right < n; right++) {
                if (s.charAt(right) == '(') {
                    leftCount++;
                    continue;
                }

                rightCount++;
                if ((right - left + 1) > longest && leftCount == rightCount && this.isValid(s, left, right)) {
                    longest = Math.max(longest, right - left + 1);
                }
            }
        }
    }

    return longest;        
}
    
```

这次终于执行通过了.

执行结果:

```
Runtime: 1320 ms, faster than 5.02% of Java online submissions for Longest Valid Parentheses.
Memory Usage: 40.3 MB, less than 5.88% of Java online submissions for Longest Valid Parentheses.
```



## Solution 2. 动态规划

```java
class Solution {
    public int longestValidParentheses(String s) {
        int longest = 0;
        int n = s.length();                
        
        int[] dp = new int[n];        
        
        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == '(') {
                continue;
            }
            // 第一种情况上一个字符是(
            if (s.charAt(i - 1) == '(') {
                dp[i] = (i -2 >= 0 ? dp[i - 2] : 0) + 2;
            }
            // 第二种情况, 上一个字符是 )
            else {
                // (()())
                //      i                 
                if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + 2;
                    // 检测 i - dp[i - 1] - 1, 即(位置前面是否有正确的连续字符
                    // () (()())
                    //  j      i    是否就是看是否存在j这种情况
                    int j = i - dp[i - 1] - 2;
                    if (j >= 0 && dp[j] > 0) {
                        dp[i] = dp[j] + dp[i];
                    }                    
                }                
            }
            longest = Math.max(longest, dp[i]);
        }
        
        
        return longest;        
    }
}
```

```java
Runtime: 1 ms, faster than 100.00% of Java online submissions for Longest Valid Parentheses.
Memory Usage: 39.6 MB, less than 5.88% of Java online submissions for Longest Valid Parentheses.
```

## Solution 3 Stack

```java
class Solution {
    public int longestValidParentheses(String s) {
        int longest = 0;
           
        Stack<Integer> stack = new Stack<>();     
        stack.push(-1);
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.push(i);
            } else {
                stack.pop();
                if (stack.empty()) {
                    stack.push(i);
                } else {
                    longest = Math.max(longest, i - stack.peek());
                }
            }
        }
        
        return longest;        
    }
}
```

执行结果

```
Runtime: 2 ms, faster than 66.61% of Java online submissions for Longest Valid Parentheses.
Memory Usage: 39.5 MB, less than 5.88% of Java online submissions for Longest Valid Parentheses.

```

