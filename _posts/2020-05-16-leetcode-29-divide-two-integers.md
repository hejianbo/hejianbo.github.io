---
layout: post
title: 每日一题 - 29.Divide Two Integers
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 29.Divide Two Integers
---

## 29.Divide Two Integers

https://leetcode.com/problems/divide-two-integers/

## Description

Given two integers `dividend` and `divisor`, divide two integers without using multiplication, division and mod operator. (不允许用乘法、除法、取模)

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero, which means losing its fractional part. For example, `truncate(8.345) = 8` and `truncate(-2.7335) = -2`.

**Example 1:**

```
Input: dividend = 10, divisor = 3
Output: 3
Explanation: 10/3 = truncate(3.33333..) = 3.
```

**Example 2:**

```
Input: dividend = 7, divisor = -3
Output: -2
Explanation: 7/-3 = truncate(-2.33333..) = -2.
```

**Note:**

- Both dividend and divisor will be 32-bit signed integers.
- The divisor will never be 0.
- Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of this problem, assume that your function **returns 231 − 1 when the division result overflows**.

## Solution 1. 累加 [Time Limit Exceeded]

解法1是直接转为加法, 然后进行累加.

```java
class Solution {
    public int divide(int dividend, int divisor) { 
        int factor = 1;
        if (dividend > 0 && divisor < 0 || dividend < 0 && divisor > 0) {
            factor = -1;
        }
        if (divisor == -1 && dividend == Integer.MIN_VALUE) {
            return Integer.MAX_VALUE;
        }
        return this.helper(Math.abs((long)dividend), Math.abs((long)divisor)) * factor;
    }
    
    private int helper(long dividend, long divisor) {
        if (dividend < divisor) {
            return 0;
        }
        if (dividend == divisor) {
            return 1;
        }
        
        int result = 0;        
        while (divisor <= dividend) {
            dividend = dividend - divisor;
            result += 1;
        }
        
        return result;
    }
}
```



## Solution 2 优化

直接基于上面的答案进行优化. 主要是加快累加的速度, 每次累加都翻倍.

```java
class Solution {
    public int divide(int dividend, int divisor) { 
        int factor = 1;
        if (dividend > 0 && divisor < 0 || dividend < 0 && divisor > 0) {
            factor = -1;
        }
        if (divisor == -1 && dividend == Integer.MIN_VALUE) {
            return Integer.MAX_VALUE;
        }
        return this.helper(Math.abs((long)dividend), Math.abs((long)divisor)) * factor;
    }
    
    private int helper(long dividend, long divisor) {
        if (dividend < divisor) {
            return 0;
        }
        if (dividend == divisor) {
            return 1;
        }
        
        long step = divisor;
        int stepValue = 1;
        
        int result = 0;        
        while (divisor <= dividend) {
            if (dividend - step >= 0) {
                dividend = dividend - step;
                result += stepValue;
                // step翻倍
                step += step;
                stepValue += stepValue;
            } else {
                step = divisor;
                stepValue = 1;
            }
        }
        
        return result;
    }
}
```

```java
Runtime: 1 ms, faster than 100.00% of Java online submissions for Divide Two Integers.
Memory Usage: 37 MB, less than 6.06% of Java online submissions for Divide Two Integers.
```

