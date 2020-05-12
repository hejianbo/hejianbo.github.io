---
layout: post
title: 每日一题 - 179.Largest Number
tags:
- leetcode
- Medium
- 排序
categories: leetcode
description: LeetCode 179.Largest Number
---
# 179.Largest Number

https://leetcode.com/problems/largest-number/

这题很显示的是在考察字符串的排序.

## Description

Given a list of non negative integers, arrange them such that they form the largest number.

**Example 1:**

```
Input: [10,2]
Output: "210"
```

**Example 2:**

```
Input: [3,30,34,5,9]
Output: "9534330"
```

**Note:** The result may be very large, so you need to return a string instead of an integer.



## Solution

直接利用java内置的字符串排序.

```java
class Solution {
    public String largestNumber(int[] nums) {
        String value = Arrays.stream(nums)
            .mapToObj(item -> String.valueOf(item))
            .sorted((a, b) -> (b + a).compareTo(a + b))
            .collect(Collectors.joining());    
        return value.startsWith("0") ? "0" : value;
    }
}
```

执行结果

```
Runtime: 9 ms, faster than 28.60% of Java online submissions for Largest Number.
Memory Usage: 39 MB, less than 6.67% of Java online submissions for Largest Number.
```

