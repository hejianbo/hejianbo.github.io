---
layout: post
title: 每日一题 - 315.Count of Smaller Numbers After Self 
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 315.Count of Smaller Numbers After Self 
---

# 315.Count of Smaller Numbers After Self 

https://leetcode.com/problems/count-of-smaller-numbers-after-self/

## Description

You are given an integer array *nums* and you have to return a new *counts* array. The *counts* array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

**Example:**

```
Input: [5,2,6,1]
Output: [2,1,1,0] 
Explanation:
To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.
```



## Solution 1

```java
class Solution {
    public List<Integer> countSmaller(int[] nums) {
        if (nums == null || nums.length == 0) {
            return new ArrayList<Integer>();
        }
        
        Integer[] answer = new Integer[nums.length];
        Arrays.fill(answer, 0);
        
        for (int i = nums.length - 2; i >= 0; i--) {
            int count = 0;
            for (int j = i + 1; j < nums.length; j++) {                
                if (nums[j] < nums[i]) {
                    count++;
                } else if (nums[j] == nums[i]) {
                    count += answer[j];
                    break;
                }
            }            
            answer[i] = count;
        }
        
        return Arrays.asList(answer);
        
    }
}
```

执行结果

```
Runtime: 581 ms, faster than 24.07% of Java online submissions for Count of Smaller Numbers After Self.
Memory Usage: 43.9 MB, less than 5.55% of Java online submissions for Count of Smaller Numbers After Self.

```

##  Solution 2 BinarySearch

利用Java的Collections.BinarySearch找到插入点. 

- 如果找到了元素，将会返回对应的索引位置　
- 如果没有找到会返回 [-(插入点) - 1]

```java
class Solution {
    public List<Integer> countSmaller(int[] nums) {
        if (nums == null || nums.length == 0) {
            return new ArrayList<Integer>();
        }
        int n = nums.length;
        
        List<Integer> answer = new ArrayList<>();
        answer.add(0);
        
        // 维持顺序
        List<Integer> sortedList = new ArrayList<>();        
        sortedList.add(nums[n - 1]);
        
        for (int i = n - 2; i >= 0; i--) {
            // 根据binarySearch得到插入点, 正负值都可能
            // (-(insertion point) - 1
            int insertIndex = Collections.binarySearch(sortedList, nums[i]);            
            int absInsertIndex = insertIndex < 0 ? Math.abs(insertIndex + 1) : insertIndex;
            
            if (insertIndex < 0) {
                answer.add(0, absInsertIndex);           
            } else {         
                // 找到第一个不相等的元素
                // 因为absInsertIndex前面的元素只能表示<=它, 所以必须把等于排除掉
                boolean hasLower = false;
                for (int j = absInsertIndex; j >= 0; j--) {
                    if (sortedList.get(j) != nums[i]) {
                        answer.add(0, j + 1);
                        hasLower = true;
                        break;
                    }
                }
                if (!hasLower) {
                    answer.add(0, 0);
                }
            }
            
            // 将当前元素插入
            sortedList.add(absInsertIndex, nums[i]);
        }
        
        return answer;
        
    }
}
```

执行结果

```
Runtime: 40 ms, faster than 29.12% of Java online submissions for Count of Smaller Numbers After Self.
Memory Usage: 46.1 MB, less than 5.55% of Java online submissions for Count of Smaller Numbers After Self.
```



