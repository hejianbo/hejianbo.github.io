---
layout: post
title: 每日一题 - 324.Wiggle Sort II
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 324.Wiggle Sort II
---

# 324.Wiggle Sort II

https://leetcode.com/problems/wiggle-sort-ii/



## Description

Given an unsorted array `nums`, reorder it such that `nums[0] < nums[1] > nums[2] < nums[3]...`.

**Example 1:**

```
Input: nums = [1, 5, 1, 1, 6, 4]
Output: One possible answer is [1, 4, 1, 5, 1, 6].
```

**Example 2:**

```
Input: nums = [1, 3, 2, 2, 3, 1]
Output: One possible answer is [2, 3, 1, 3, 1, 2].
```

**Note:**
You may assume all input has valid answer.

**Follow Up:**
Can you do it in O(n) time and/or in-place with O(1) extra space?



## Solution 

这个解因为需要排序, 所以初始就超过O(n)的时间复杂度了, 空间因为进行了拷贝整个数组, 所以是O(n).

```java
class Solution {
    public void wiggleSort(int[] nums) {     
        int n = nums.length;
        if (nums == null || n< 2) {
            return;
        }
        // 复制然后排序
        int[] temp = nums.clone();        
        Arrays.sort(temp);
        
        int low = 0;
        int middle = (n - 1) / 2;
        int hi = middle + 1;
        
        // 将元素小的一半倒序排在索引为偶数的位置 
        int i = 0;
        for(int j = middle; j >= 0; j--) {
            nums[i] = temp[j];
            i += 2;
        }
        // 将元素大的一半倒序排在索引为奇数的位置
        i = 1;
        for (int j = n - 1; j > middle; j--) {
            nums[i] = temp[j];
            i += 2;
        }
    }
}
```

执行结果

```
Runtime: 6 ms, faster than 42.47% of Java online submissions for Wiggle Sort II.
Memory Usage: 54.4 MB, less than 10.00% of Java online submissions for Wiggle Sort II.
```

