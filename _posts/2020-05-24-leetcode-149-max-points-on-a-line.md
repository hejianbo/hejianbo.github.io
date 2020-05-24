---
layout: post
title: 每日一题 - 149.Max Points on a Line
tags:
- leetcode
- Hard
- 最大公约数
- 分数
categories: leetcode
description: LeetCode 149.Max Points on a Line
---

# 149.Max Points on a Line

https://leetcode.com/problems/max-points-on-a-line/

## Description

Given *n* points on a 2D plane, find the maximum number of points that lie on the same straight line.

**Example 1:**

```
Input: [[1,1],[2,2],[3,3]]
Output: 3
Explanation:
^
|
|        o
|     o
|  o  
+------------->
0  1  2  3  4
```

**Example 2:**

```
Input: [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
Explanation:
^
|
|  o
|     o        o
|        o
|  o        o
+------------------->
0  1  2  3  4  5  6
```

**NOTE:** input types have been changed on April 15, 2019. Please reset to default code definition to get new method signature.



## Solution 通过斜率表示共线

这道题从代码的角度来看其实是一道蛮简单的题, 主要考察了下面几点:

- 如何算共线, 这里使用的是直线的斜率, 然后又有一个公共起点, 所以肯定就共线了
- 在计算斜率的时候会出现除不尽的情况, 即精度问题, 用double表示是不行的, 尝试过用BigDecimal, 貌似Leetcode不支持
- 所以最后精度问题是通过分数来表示的,  用分数表示就得计算**最大公约数**, 这个算法直接在网上找的.

``` java
class Solution {
    public int maxPoints(int[][] points) {
        if (points == null || points.length == 0) {
            return 0;
        }
        if (points.length == 1) {
            return 1;
        }

        int max = 0;
        for (int i = 0; i < points.length; i++) {
            // 在一条线上的点斜率一点一样
            // 斜率 = (y1 - y2) / x1 - x2                
            Map<Fraction, Integer> map = new HashMap<>();
            // 初始值为1表示当前这个结点
            int duplicate = 1;
            int currentMax = 0;
            // 用来表示垂直于x轴的直线
            int vertical = 0;
            for (int j = i + 1; j < points.length; j++) {
                int x1 = points[i][0];
                int y1 = points[i][1];
                int x2 = points[j][0];
                int y2 = points[j][1];

                // 相同点跳过, 不处理
                if (x1 == x2 && y1 == y2) {
                    duplicate++;
                    continue;
                }
                if (x1 == x2) {
                    vertical += 1;
                    continue;
                }

                // 计算斜率
                Fraction slope = new Fraction(y1 - y2, x1 - x2);
                map.put(slope, map.getOrDefault(slope, 0) + 1);
                currentMax = Math.max(currentMax, map.get(slope));
            }
            max = Math.max(max, Math.max(vertical, currentMax) + duplicate);
        }

        return max;
    }

    static class Fraction {
        int numerator;
        int denominator;
        Fraction(int numerator, int denominator) {
            // 先求最大公约数, 进行约分
            int gcd = this.greatestCommonDivisor(numerator, denominator);
            // 通过最大公约数进行约分
            this.numerator = numerator / gcd;
            this.denominator = denominator / gcd;
        }
        
        private int greatestCommonDivisor(int a, int b) {
            return b == 0 ? a : this.greatestCommonDivisor(b, a % b);
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Fraction fraction = (Fraction) o;
            return numerator == fraction.numerator && denominator == fraction.denominator;
        }

        @Override
        public int hashCode() {
            return Objects.hash(numerator, denominator);
        }
    }
}
```

执行结果

```
Runtime: 11 ms, faster than 79.94% of Java online submissions for Max Points on a Line.
Memory Usage: 39.5 MB, less than 20.00% of Java online submissions for Max Points on a Line.

```

