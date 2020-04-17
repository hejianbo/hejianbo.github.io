---
layout: post
title: 每日一题 - LeetCode 239.Sliding Window Maximum
tags:
- leetcode
- 动态规划
categories: leetcode
description: LeetCode 239.Sliding Window Maximum
---

# 239.Sliding Window Maximum

https://leetcode.com/problems/sliding-window-maximum/

此题感觉难度应该在easy或者medium, 而不是hard.

## Description

Given an array *nums*, there is a sliding window of size *k* which is moving from the very left of the array to the very right. You can only see the *k* numbers in the window. Each time the sliding window moves right by one position. Return the max sliding window.

**Follow up:**
Could you solve it in linear time?

**Example:**

```
Input: nums = [1,3,-1,-3,5,3,6,7], and k = 3
Output: [3,3,5,5,6,7] 
Explanation: 

Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

 

**Constraints:**

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `1 <= k <= nums.length`



## Solution 最大堆

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {        
        if (nums == null || nums.length == 0) {
            return new int[0];
        }
        
        int[] answer = new int[Math.max(1, nums.length - (k - 1))];        
        Queue<Integer> queue = new PriorityQueue<>(Comparator.reverseOrder());        
        int j = 0;
        for (int i = 0; i < nums.length; i++) {            
            queue.offer(nums[i]);
            if (i >= k - 1) {                
                answer[j++] = queue.peek();
                queue.remove(nums[i - (k - 1) ]);
            }
        }
        if (nums.length < k) {
            answer[0] = queue.poll();
        }
        
        return answer;
    }
}
```

执行结果

```
Runtime: 81 ms, faster than 7.91% of Java online submissions for Sliding Window Maximum.
Memory Usage: 49.1 MB, less than 6.25% of Java online submissions for Sliding Window Maximum.

```

