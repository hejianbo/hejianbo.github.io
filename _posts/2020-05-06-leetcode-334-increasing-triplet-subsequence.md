---
layout: post
title: 每日一题 - 334.Increasing Triplet Subsequence
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 334.Increasing Triplet Subsequence
---

# 334.Increasing Triplet Subsequence

https://leetcode.com/problems/increasing-triplet-subsequence/



## Description

Given an unsorted array return whether an increasing subsequence of length 3 exists or not in the array.

Formally the function should:

> Return true if there exists *i, j, k* 
> such that *arr[i]* < *arr[j]* < *arr[k]* given 0 ≤ *i* < *j* < *k* ≤ *n*-1 else return false.

**Note:** Your algorithm should run in O(*n*) time complexity and O(*1*) space complexity.

**Example 1:**

```
Input: [1,2,3,4,5]
Output: true
```

**Example 2:**

```
Input: [5,4,3,2,1]
Output: false
```



## Solution 1 回溯法

回溯, 相当于暴力解法，不停穷举，直到满足结果为止.

```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int n = nums.length;
        if (n < 3) {
            return false;
        }
        // 以每个元素为起点, 如果找到满足条件的元素超过3个, 则停止
        for (int i = 0; i < nums.length; i++) {
            if(this.helper(nums, i, i + 1, 1)) {
                return true;
            }
        }
        
        return false;
    }
    
    private boolean helper(int[] nums, int prev, int start, int count) {        
        if (count >= 3) {
            return true;
        }        
        // 找完了则中止
        if (start >= nums.length) {
            return false;
        }
        
        if(nums[start] > nums[prev] && this.helper(nums, start, start + 1, count + 1)) {
            return true;
        }                
        
        // 否则, 跳过该字符(回溯)， 继续看下一个字符
        return this.helper(nums, prev, start + 1, count);               
    }
}
```

执行结果

```
Runtime: 494 ms, faster than 5.14% of Java online submissions for Increasing Triplet Subsequence.
Memory Usage: 41 MB, less than 6.98% of Java online submissions for Increasing Triplet Subsequence.
```

下面开始基于这个解法进行优化.

对于这种会导致重复计算的问题，首先想到的第一步就是加上缓存, 而要怎么加缓存呢?

在这里我首先尝试了直接暴力的加缓存，即直接缓存helper方法, 加上cache\[prev\]\[count\], 这样加上后，发现更慢了.

```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int n = nums.length;
        if (n < 3) {
            return false;
        }
        int[][] cache = new int[n][3];
        // 以每个元素为起点, 如果找到满足条件的元素超过3个, 则停止
        for (int i = 0; i < nums.length; i++) {
            if(this.helper(cache, nums, i, i + 1, 1)) {
                return true;
            }
        }
        
        return false;
    }
    
    private boolean helper(int[][] cache, int[] nums, int prev, int start, int count) {        
        if (count >= 3) {
            return true;
        }        
        // 找完了则中止
        if (start >= nums.length) {
            return false;
        }
        
        // 直接返回false的原因, 如果为true了, 表示已经有解了, 则已经返回了, 所以返回的肯定是false
        if (cache[prev][count] != 0) {
            return false;
        }
        
        if(nums[start] > nums[prev] && this.helper(cache, nums, start, start + 1, count + 1)) {
            return true;
        }                
        
        // 否则, 跳过该字符(回溯)， 继续看下一个字符
        cache[prev][count] = this.helper(cache, nums, prev, start + 1, count) ? 1 : -1;        
        return cache[prev][count] == 1;
    }
}
```

执行结果

```
Runtime: 955 ms, faster than 5.14% of Java online submissions for Increasing Triplet Subsequence.
Memory Usage: 40.5 MB, less than 6.98% of Java online submissions for Increasing Triplet Subsequence.
```

可以看到更慢了, 因为缓存基本没啥效果，反而增加了开销, 不过在做这个的时候，想到了下面的解法.

## Solution 2

这个解是在上面做优化的时候, 突然想到的.  在做上面的优化的时候, 加缓存的时候想到能不能把后面有多长的子序列缓存下来， 然后就觉得直接缓存每个字符前面有多长的子序列也是一样的.

```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int n = nums.length;
        if (n < 3) {
            return false;
        }
        
        // 表示前面有多少个元素比自己小
        int[] cache = new int[n];
        
        for (int i = 1; i < n; i++) {            
            for (int j = i - 1; j >= 0; j--) {
                // 如果i比前面的j大, 则i的数量应试比j的数量大1
                if (nums[i] > nums[j]) {
                    cache[i] = Math.max(cache[j] + 1, cache[i]);
                } else if (nums[i] == nums[j]) {                    
                    cache[i] = Math.max(cache[j], cache[i]);
                    break;
                }
            }
            // 比自己小的有序元素 + 1即为总长度
            if (cache[i] + 1 >= 3) {
                return true;
            }
        }
        
        return false;
    }
}
```

执行结果

```
Runtime: 87 ms, faster than 5.14% of Java online submissions for Increasing Triplet Subsequence.
Memory Usage: 39.3 MB, less than 93.02% of Java online submissions for Increasing Triplet Subsequence.
```

