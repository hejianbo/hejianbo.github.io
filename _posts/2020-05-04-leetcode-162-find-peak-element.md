---
layout: post
title: 每日一题 - 162.Find Peak Element
tags:
- leetcode
- 二分查找
- Medium
categories: leetcode
description: LeetCode 162.Find Peak Element
---

# 162.Find Peak Element

https://leetcode.com/problems/find-peak-element/



A peak element is an element that is greater than its neighbors.

Given an input array `nums`, where `nums[i] ≠ nums[i+1]`, find a peak element and return its index.

The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.

You may imagine that `nums[-1] = nums[n] = -∞`.

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
```

**Example 2:**

```
Input: nums = [1,2,1,3,5,6,4]
Output: 1 or 5 
Explanation: Your function can return either index number 1 where the peak element is 2, 
             or index number 5 where the peak element is 6.
```

**Note:**

Your solution should be in logarithmic complexity.



## Solution 二分查找

其实这题根据Note提示, 要求log的时间复杂度, 就相当于暗示了使用二份查找的解法.  先看一下最开始自己写的大于log时间度的解法.

```java
class Solution {
    public int findPeakElement(int[] nums) {
        if (nums.length == 1) {
            return 0;
        }
        return this.find(nums, 0, nums.length - 1);
    }
    
    private int find(int[] nums, int start, int end) {
        if (start > end) {
            return -1;
        }
        
        int middle = start + (end - start) / 2;                
        if ((middle == nums.length - 1 || nums[middle] > nums[middle + 1])
            && (middle == 0 || nums[middle] > nums[middle - 1])) {
            return middle;
        }
        int position = this.find(nums, start, middle - 1);
        if (position >= 0) {
            return position;
        }
        position = this.find(nums, middle + 1, end);
        if (position >= 0) {
            return position;
        }
        return -1;
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Find Peak Element.
Memory Usage: 39.5 MB, less than 56.72% of Java online submissions for Find Peak Element.
```

上面这个解的时间复杂度肯定不止log的复杂度的,  看下面这个解, 这是从leetcode看到的, 初看的时候感觉可能不正确, 但仔细思考一下, 发现是正确的.

```java
class Solution {
    public int findPeakElement(int[] nums) {
        return this.find(nums, 0, nums.length - 1);
    }
    
    private int find(int[] nums, int start, int end) {   
        // 如果只有一个元素的情况, 它自己就是山峰
        // 两种情况
        // 1. 如果一直是在二分的最左边, 即到了边界索引为0的位置, 则它肯定是山峰
        // 2. 如果不是, 则表示start比start-1大, 而start==end > end + 1, 所以它就是山峰
        if (start == end) {
            return start;
        }
        
        int middle = start + (end - start) / 2;   
        // 如果middle比左边元素大, 所以左边一定有山峰
        // 因为在： -∞ < nums[middle] > nums[middle + 1];
        // 所以在这之间一定形成山峰
        if (nums[middle] > nums[middle + 1]) {
            return this.find(nums, start, middle);
        }
        // 同理在 nums[middle] < nums[middle + 1] > -∞ 之间
        // 也一定定形成山峰, 
        // 这里注意前提条件: nums[i] != nums[i+1] 
        return this.find(nums, middle + 1, end);
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Find Peak Element.
Memory Usage: 39.5 MB, less than 64.18% of Java online submissions for Find Peak Element.
```

