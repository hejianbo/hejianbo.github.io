---
layout: post
title: 每日一题 - 50.Pow(x, n)
tags:
- leetcode
- Medium
- 分治
categories: leetcode
description: LeetCode 50.Pow(x, n)
---

# 50.Pow(x, n)

https://leetcode.com/problems/powx-n/

## Description

Implement [pow(*x*, *n*)](http://www.cplusplus.com/reference/valarray/pow/), which calculates *x* raised to the power *n* (xn).

**Example 1:**

```
Input: 2.00000, 10
Output: 1024.00000
```

**Example 2:**

```
Input: 2.10000, 3
Output: 9.26100
```

**Example 3:**

```
Input: 2.00000, -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25
```

**Note:**

- -100.0 < *x* < 100.0
- *n* is a 32-bit signed integer, within the range [−231, 231 − 1]



## Solution 1 暴力

```java
class Solution {
    public double myPow(double x, int n) {
        double product = 1;
        for (int i = 0; i < Math.abs(n); i++) {
            product *= x;
        }        
        
        return n < 0 ? 1/product : product;
    }
}
```

## Solution 2 分治

上面的解由于是o(n), 给出的测试用例太大, 所以超时了, 所以需要对他进行分解, 下面的解应该是O(logn)

```java
class Solution {
    public double myPow(double x, int n) {       
        double product = this.divideAndConquer(x, Math.abs(n));
        return n < 0 ? 1 / product : product;
    }
    
    private double divideAndConquer(double x, int n) {      
        if (n == 0) {
            return 1.0;
        }
        if (n == 1) {
            return x;
        }
        if (n == 2) {
            return x * x;
        }
        
        int half = n / 2;
        // 计算一半
        double product = this.divideAndConquer(x, half);
        // 如果是偶数, 则刚好还剩一半, 再乘一次就可以了
        if (n % 2 == 0) {
            return product * product;
        } else {
            // 如果是积数, 则比偶数的情况还要多一次, 所以还需要再乘一个x
            return product * product * x;
        }        
    }
}
```



```java
Runtime: 0 ms, faster than 100.00% of Java online submissions for Pow(x, n).
Memory Usage: 36.7 MB, less than 5.88% of Java online submissions for Pow(x, n).
```

> 这么简单的题都会出现错误............. 没有ide总是拼写错误, 忘记标点符号(这肯定是python写多了)等, 提醒一下自己以后一定要仔细.