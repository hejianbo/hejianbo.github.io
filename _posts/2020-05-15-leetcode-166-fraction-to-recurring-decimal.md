---
layout: post
title: 每日一题 - 166.Fraction to Recurring Decimal 
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 166.Fraction to Recurring Decimal 
---

# 166.Fraction to Recurring Decimal 

https://leetcode.com/problems/fraction-to-recurring-decimal/

## Description

Given two integers representing the numerator and denominator of a fraction, return the fraction in string format.

If the fractional part is repeating, enclose the repeating part in parentheses.

**Example 1:**

```
Input: numerator = 1, denominator = 2
Output: "0.5"
```

**Example 2:**

```
Input: numerator = 2, denominator = 1
Output: "2"
```

**Example 3:**

```
Input: numerator = 2, denominator = 3
Output: "0.(6)"
```



## Solution

这道题在找重复的时候, 一定要明白这几点:

1. 分母是不变的
2. 当分子出现重复的时候即表示开始重复了
3. 所以重点是缓存分子, 当分子出现重复时, 即出现了解.

```java
class Solution {
    public String fractionToDecimal(int numerator, int denominator) {
        StringBuilder answer = new StringBuilder();
        if (numerator == 0) {
            return "0";
        }
        if (numerator > 0 && denominator < 0 || denominator > 0 && numerator < 0) {
            answer.append("-");
        }

        // 分子
        Long a = Math.abs(Long.valueOf(numerator));
        // 分母
        Long b = Math.abs(Long.valueOf(denominator));
        // 整数部分
        answer.append(a / b);

        // 是否存在余数, 如果不存在, 表示直接整除了
        Long remainder = a % b;
        if (remainder == 0) {
            return answer.toString();
        }

        // 添加小数点
        answer.append(".");

        // 用来检测是否重复
        Map<Long, Integer> map = new HashMap<>();
        // 保存余数
        StringBuilder divides = new StringBuilder();
        int index = 0;

        while (true) {
            // 如果已经包含该余数, 则表示重复了
            if (map.containsKey(remainder)) {
                index = map.get(remainder);
                break;
            } else {
                map.put(remainder, divides.length());
            }

            // 除后的结果
            long divideResult = (remainder * 10) / b;
            divides.append(divideResult);

            // 新的余数
            remainder = (remainder * 10) % b;
            // 如果余数为0, 表示整除了
            if (remainder == 0) {
                return answer.append(divides.toString()).toString();
            }
        }
        if (index > 0) {
            answer.append(divides.substring(0, index));
        }
        answer.append("(");
        answer.append(divides.substring(index, divides.length()));
        answer.append(")");
        return answer.toString();
    }

}
```

执行结果

```
Runtime: 1 ms, faster than 99.73% of Java online submissions for Fraction to Recurring Decimal.
Memory Usage: 36.7 MB, less than 9.52% of Java online submissions for Fraction to Recurring Decimal.
```

