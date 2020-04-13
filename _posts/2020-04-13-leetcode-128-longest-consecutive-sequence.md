---
layout: post
title: 每日一题 - LeetCode 128.Longest Consecutive Sequence
tags:
- leetcode
categories: leetcode
description: LeetCode 128.Longest Consecutive Sequence
---

# 128.Longest Consecutive Sequence

## Description

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

Your algorithm should run in O(*n*) complexity.

**Example:**

```
Input: [100, 4, 200, 1, 3, 2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```



## Solution 1 HashMap

这个解最坏的情况下不是o(n);, 里面嵌入了一个while循环. 看了leetcode的暴力解法, 这个其实就是暴力解法.

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int n = nums.length;
        if (nums == null || n == 0) {
            return 0;
        }
                        
        Map<Integer, Integer> map = new HashMap<>();
        int longest = 0;
        for (int i = 0; i < n; i++) {
            if (map.containsKey(nums[i])) {
                continue;
            }
            
            //向前检测
            int consecutive = 1;
            if (map.containsKey(nums[i] - 1)) {
                consecutive += map.get(nums[i] - 1);
            }
            map.put(nums[i], consecutive);
            
            // 向后检测
            int successor = nums[i] + 1;
            while (map.containsKey(successor)) {                
                map.put(successor++, ++consecutive);
            }
            
            longest = Math.max(longest, consecutive);
        }
        
        return longest;
    }
}
```

执行结果

```
Runtime: 1551 ms, faster than 5.00% of Java online submissions for Longest Consecutive Sequence.
Memory Usage: 41 MB, less than 5.17% of Java online submissions for Longest Consecutive Sequence.
```



## Solution2 排序

这个和上面一样, 不能达到o(n), 主要是排序，一般排序都是快排也需要o(nlogn), 所以这个解预处理是就已经超过o(n)时间复杂度了

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int n = nums.length;
        if (nums == null || n == 0) {
            return 0;
        }
                        
        Arrays.sort(nums);
        int longest = 1;
        int current = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i - 1] == nums[i]) {
                continue;
            }
            if (nums[i] - 1 == nums[i - 1]) {                
                longest = Math.max(++current, longest);
            } else {
                current = 1;
            }       
        }
        
        return longest;
    }
}
```

执行结果

```
Runtime: 2 ms, faster than 100.00% of Java online submissions for Longest Consecutive Sequence.
Memory Usage: 40.1 MB, less than 13.80% of Java online submissions for Longest Consecutive Sequence.
```

## Solution 3 HashSet

与解法1很相近,  目的就是在循环内消除不必要重复计算. 这个解是可以达到o(n)的

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int n = nums.length;
        if (nums == null || n == 0) {
            return 0;
        }
        
        Set<Integer> set = new HashSet<Integer>();
        for (int num : nums) {
            set.add(num);
        }
        
        int longest = 0;
        for (int num : set) {
            if (!set.contains(num - 1)) {
                int secutive = 1;
                int successor = num + 1;
                while (set.contains(successor)) {
                    secutive++;
                    successor++;
                }
                longest = Math.max(secutive, longest);
            }
        }
        
        return longest;
    }
}
```

执行结果

```
Runtime: 3 ms, faster than 91.81% of Java online submissions for Longest Consecutive Sequence.
Memory Usage: 40 MB, less than 13.80% of Java online submissions for Longest Consecutive Sequence.
```



## Solution 4 暴力

与解法1基本是一致的

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int n = nums.length;
        if (nums == null || n == 0) {
            return 0;
        }
        
        int longest = 0;
        
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        
        for (int i = 0; i < n; i++) {
            int consecutive = 1;
            int successor = nums[i] + 1;
            while (set.contains(successor)) {
                consecutive += 1;
                successor += 1;
            }
            longest = Math.max(longest, consecutive);
        }
        
        return longest;
    }
}
```

执行结果

```
Runtime: 665 ms, faster than 7.60% of Java online submissions for Longest Consecutive Sequence.
Memory Usage: 41.2 MB, less than 5.17% of Java online submissions for Longest Consecutive Sequence.
```

