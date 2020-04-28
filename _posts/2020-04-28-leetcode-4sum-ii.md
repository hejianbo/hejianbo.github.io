---
layout: post
title: 每日一题 - 454.4Sum II
tags:
- leetcode
categories: leetcode
description: LeetCode 454.4Sum II
---

# 454.4Sum II

https://leetcode.com/problems/4sum-ii/

## Description

Given four lists A, B, C, D of integer values, compute how many tuples `(i, j, k, l)` there are such that `A[i] + B[j] + C[k] + D[l]` is zero.

To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -228 to 228 - 1 and the result is guaranteed to be at most 231 - 1.

**Example:**

```
Input:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

Output:
2

Explanation:
The two tuples are:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```



## Solution 1 暴力 (Time Limit Exceeded)

```java
class Solution {
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        
        int count = 0;
        for (int i = 0; i < A.length; i++) {
            for (int j = 0; j < B.length; j++) {
                for (int n = 0; n < C.length; n++) {
                    for (int m = 0; m < D.length; m++) {
                        if (A[i] + B[j] + C[n] + D[m] == 0) {
                            count++;       
                        }
                    }
                }
            }
        }
        
        return count;
    }
}
```



## Solution 2

```java
class Solution {
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        Map<Integer, Integer> map = new HashMap<>();   
        for (int i = 0; i < A.length; i++) {
            for (int j = 0; j < B.length; j++) {
                int sum = A[i] + B[j];
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
        }
        
        int count = 0;
        for (int n = 0; n < C.length; n++) {
            for (int m = 0; m < D.length; m++) {
                int sum = C[n] + D[m];
                int key = 0 - sum;
                count += map.getOrDefault(key, 0);
            }
        }
            
        return count;
    }
}
```

执行结果

```
Runtime: 64 ms, faster than 61.94% of Java online submissions for 4Sum II.
Memory Usage: 59.1 MB, less than 52.00% of Java online submissions for 4Sum II.
```

