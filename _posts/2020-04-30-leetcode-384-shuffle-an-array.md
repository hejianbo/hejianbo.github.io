---
layout: post
title: 每日一题 - 384.Shuffle an Array
tags:
- leetcode
categories: leetcode
description: LeetCode 384.Shuffle an Array
---

# 384.Shuffle an Array

https://leetcode.com/problems/shuffle-an-array/

## Description

Shuffle a set of numbers without duplicates.

**Example:**

```
// Init an array with set 1, 2, and 3.
int[] nums = {1,2,3};
Solution solution = new Solution(nums);

// Shuffle the array [1,2,3] and return its result. Any permutation of [1,2,3] must equally likely to be returned.
solution.shuffle();

// Resets the array back to its original configuration [1,2,3].
solution.reset();

// Returns the random shuffling of array [1,2,3].
solution.shuffle();
```



## Solution

```java
class Solution {
    final private int[] orginNums;
        
    public Solution(int[] nums) {
        this.orginNums = nums;
    }
    
    /** Resets the array to its original configuration and return it. */
    public int[] reset() {
        return this.orginNums;
    }
    
    /** Returns a random shuffling of the array. */
    public int[] shuffle() {
        int[] copy = this.orginNums.clone();
        
        Random random = new Random();
        for (int i = copy.length; i >= 1; i--) {
            int index = random.nextInt(i);            
            this.swap(copy, index, i - 1);            
        }
        
        return copy;
    }
    
    private void swap(int[] nums, int i, int j) {
        int a = nums[i];
        nums[i] = nums[j];
        nums[j] = a;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int[] param_1 = obj.reset();
 * int[] param_2 = obj.shuffle();
 */
```

执行结果

```
Runtime: 79 ms, faster than 58.95% of Java online submissions for Shuffle an Array.
Memory Usage: 47.3 MB, less than 100.00% of Java online submissions for Shuffle an Array.

```

