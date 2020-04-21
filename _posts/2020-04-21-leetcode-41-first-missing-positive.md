---
layout: post
title: 每日一题 - 41.First Missing Positive
tags:
- leetcode
categories: leetcode
description: LeetCode 41.First Missing Positive
---
# 41.First Missing Positive

https://leetcode.com/problems/first-missing-positive/


## Description

Given an unsorted integer array, find the smallest missing positive integer.

**Example 1:**

```
Input: [1,2,0]
Output: 3
```

**Example 2:**

```
Input: [3,4,-1,1]
Output: 2
```

**Example 3:**

```
Input: [7,8,9,11,12]
Output: 1
```

**Note:**

Your algorithm should run in *O*(*n*) time and uses constant extra space.



## Solution

虽然内部有个while, 但可以看到时间复杂度还是在O(n).

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        
        int min = 1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] >= 0) {
                set.add(nums[i]);                
                while (nums[i] == min || set.contains(min)) {
                    min += 1;
                }
            } 
        }
        
        return min;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 32.14% of Java online submissions for First Missing Positive.
Memory Usage: 37.5 MB, less than 6.85% of Java online submissions for First Missing Positive.
```

