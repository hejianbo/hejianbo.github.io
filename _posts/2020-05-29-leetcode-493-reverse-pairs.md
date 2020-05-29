---
layout: post
title: 每日一题 - 493.Reverse Pairs
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 493.Reverse Pairs
---

# 493.Reverse Pairs

https://leetcode.com/problems/reverse-pairs/

## Description

Given an array `nums`, we call `(i, j)` an **important reverse pair** if `i < j` and `nums[i] > 2*nums[j]`.

You need to return the number of important reverse pairs in the given array.

**Example1:**

```
Input: [1,3,2,3,1]
Output: 2
```

**Example2:**

```
Input: [2,4,3,5,1]
Output: 3
```

**Note:**

1. The length of the given array will not exceed `50,000`.
2. All the numbers in the input array are in the range of 32-bit integer.

## Solution 1. Brute Force (Time Limit Exceed)

```java
class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) {
           return 0;
        }
        
        int n = nums.length;
        
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] > nums[j] * 2L) {
                    count++;
                }
            }
        }
        
        return count;
        
    }
}
```

 

## Solution 2. BinarySearch

思路: 上面暴力的时候就是因为比较的次数太多了, 所以在做的时候想到从后往前处理, 后面处理完的数据就排好序, 然后再利用二分搜索来进行处理, 这样就会大大的减少比较次数.

```java
class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int n = nums.length;
        List<Long> list = new ArrayList<>();
        list.add(nums[n - 1] * 2L);

        int count = 0;
        for (int i = n - 2; i >= 0; i--) {
            long cur = nums[i];
            // 插入点, 即为它应该插入的顺序位置
            int index = Collections.binarySearch(list, cur);
            if (index < 0) {                
                index = Math.abs(index + 1);
            } else {
                while (index > 0 && list.get(index - 1) == nums[i]) {
                    index--;
                }
            }
            // index前面的值都满足条件
            count += index;            
            long cur2 = cur * 2;
            int insertIndex = Collections.binarySearch(list, cur2);
            if (insertIndex >= 0) {
                list.add(insertIndex, cur2);
            } else {
                list.add(Math.abs(insertIndex + 1), cur2);
            }
        }

        return count;
    }
}
```

执行结果

```
Runtime: 418 ms, faster than 6.74% of Java online submissions for Reverse Pairs.
Memory Usage: 47.9 MB, less than 100.00% of Java online submissions for Reverse Pairs.
```

