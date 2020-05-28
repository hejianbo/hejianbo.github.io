---
layout: post
title: 每日一题 - 862.Shortest Subarray with Sum at Least K
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 862.Shortest Subarray with Sum at Least K
---

# 862.Shortest Subarray with Sum at Least K

https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/

## Description

Return the **length** of the shortest, non-empty, contiguous subarray of `A` with sum at least `K`.

If there is no non-empty subarray with sum at least `K`, return `-1`.

 


**Example 1:**

```
Input: A = [1], K = 1
Output: 1
```

**Example 2:**

```
Input: A = [1,2], K = 4
Output: -1
```

**Example 3:**

```
Input: A = [2,-1,2], K = 3
Output: 3
```

 

**Note:**

1. `1 <= A.length <= 50000`
2. `-10 ^ 5 <= A[i] <= 10 ^ 5`
3. `1 <= K <= 10 ^ 9`



## Solution 1. Time Limit Exceed 

```java
class Solution {
    public int shortestSubarray(int[] A, int K) {
        if (A == null || A.length == 0) {
            return K == 0 ? 0 : -1;
        }
        
        int n = A.length;        
        int[] sums = new int[n + 1];
        int[] lengths = new int[n + 1];        
        lengths[0] = 0;
        sums[0] = 0;
        int minLen = Integer.MAX_VALUE;
        for (int i = 0; i < A.length; i++) {
            // 初始值
            sums[i + 1] = A[i];
            lengths[i + 1] = 1;
            
            // 如果当前元素已经满足条件, 则直接返回1
            if (A[i] >= K) {
                return 1;
            }            
            
            // 如果上一个元素和为0, 则没必要相加
            if (sums[i] <= 0) {
                continue;
            }
            
            // 如果加上上一组元素的最大和还是小于K, 则直接累加            
            if (sums[i + 1] + sums[i] < K) {
                sums[i + 1] += sums[i];
                lengths[i + 1] = lengths[i] + 1;
                continue;
            }
            if (i == 0) {
                continue;
            }
            
            // 否则, 说明有解, 但要取长度最短的
            for (int j = i - 1; j >= i - lengths[i]; j--) {
                sums[i + 1] += A[j];
                lengths[i + 1] += 1;
                // 非最优解, 舍弃
                if (lengths[i + 1] >= minLen) {
                    continue;
                }
                if (sums[i + 1] >= K) {
                    minLen = Math.min(minLen, lengths[i + 1]);
                    continue;
                }
            }
            
        }
        
        return minLen == Integer.MAX_VALUE ? -1 : minLen;
    }
}
```

## Solution 2

 这个解是看了leetcode答案写的

```java
class Solution {

    public int shortestSubarray(int[] A, int K) {
        if (A == null || A.length == 0) {
            return K == 0 ? 0 : -1;
        }
        
        int n = A.length;
        // 保存前缀n项和
        long[] sums = new long[n + 1];
        for (int i = 0; i < n; i++) {
            sums[i + 1] += sums[i] + A[i];
        }
        
        // 队列
        LinkedList<Integer> queue = new LinkedList<>();
        int minLen = n + 1;
        
        for (int i = 0; i < sums.length; i++) {
            // sums[i] <= sums[queue.getLast()]; 假设queue.getLast() = i - m
            // 表示sums[(i - m),  i], 区间的元素小于0, 则在计算的进修可以不用考虑这一区间, 因为这一区间不可能产生更短的满足条件的结果
            // 但更前面的这些元素还是有用的
            while (!queue.isEmpty() && sums[i] <= sums[queue.getLast()]) {                 
                queue.removeLast();
            }
            // 如果队列的首尾区间内元素和满足>=k时, 则产生可能解
            while (!queue.isEmpty() && sums[i] >= sums[queue.getFirst()] + K) {
                minLen = Math.min(minLen, i - queue.removeFirst());
            }     
            // 将当前元素放入队列
            queue.addLast(i);
        }
          
        
        return minLen == n + 1 ? -1 : minLen;        
    }   

}
```

执行结果

```
Runtime: 19 ms, faster than 86.88% of Java online submissions for Shortest Subarray with Sum at Least K.
Memory Usage: 49.8 MB, less than 100.00% of Java online submissions for Shortest Subarray with Sum at Least K.

```

