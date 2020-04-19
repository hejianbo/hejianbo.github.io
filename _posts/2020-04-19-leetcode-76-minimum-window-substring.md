---
layout: post
title: 每日一题 - LeetCode 76.Minimum Window Substring
tags:
- leetcode
categories: leetcode
description: LeetCode 76.Minimum Window Substring
---

# 76.Minimum Window Substring

https://leetcode.com/problems/minimum-window-substring/



## Description

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

**Example:**

```
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"
```

**Note:**

- If there is no such window in S that covers all characters in T, return the empty string `""`.
- If there is such window, you are guaranteed that there will always be only one unique minimum window in S.



## Solution 滑动窗口



```java
class Solution {
    public String minWindow(String s, String t) {
        if (s == null || s.isEmpty() || t == null || t.isEmpty() || s.length() < t.length()) {
            return "";
        }

        Map<Character, Integer> map = new HashMap<>();
        for (char c: s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }

        Map<Character, Integer> subMap = new HashMap<>();
        for (char c : t.toCharArray()) {
            subMap.put(c, subMap.getOrDefault(c, 0) + 1);
        }
        // 预检查字符串是否满足条件, 不满足则直接返回
        for (Character key : subMap.keySet()) {
            if (!map.containsKey(key)) {
                return "";
            }
            if (map.get(key) < subMap.get(key)) {
                return "";
            }
        }
        
        // 用来保存结果        
        int minLength = s.length();
        int minLeft = 0;
        int minRight = s.length() - 1;
        
        // 组成滑动窗口的左右两个指针
        int left = 0;
        int right = -1;
        // 当前匹配上的字符
        Map<Character, Integer> matchMap = new HashMap<>();
        // 用matchNums来表示是否已经完成匹配
        int matchNums = 0;
        
        // 通过left与right两个指针进滑动
        while (right < s.length()) {
            int length = right - left + 1;
            // 如果找到更短的匹配子串
            if (matchNums == t.length() && length < minLength ) {
                minLength = length;
                minLeft = left;
                minRight = right;
            }

            // 已经匹配上了, 移动左指针
            if (matchNums == t.length()) {
                char leftChar = s.charAt(left);
                if (subMap.containsKey(leftChar) ) {
                    if (matchMap.getOrDefault(leftChar, 0) <= subMap.get(leftChar)) {
                        matchNums--;
                    }
                    matchMap.put(leftChar, matchMap.get(leftChar) - 1);
                }
                left++;
            } else { // 不满足匹配，则移动右指针
                if (++right < s.length()) {
                    char rightChar = s.charAt(right);
                    if (subMap.containsKey(rightChar)) {
                        int count = matchMap.getOrDefault(rightChar, 0);
                        if (count < subMap.get(rightChar)) {
                            matchNums++;
                        }
                        matchMap.put(rightChar, count + 1);
                    }
                }
            }
        }

        if (matchNums == t.length() && left - right + 1 < minLength) {
            minLeft = left;
            minRight = right;
        }

        return s.substring(minLeft, minRight + 1);
    }
}
```

执行结果

```
Runtime: 40 ms, faster than 12.06% of Java online submissions for Minimum Window Substring.
Memory Usage: 47.4 MB, less than 5.32% of Java online submissions for Minimum Window Substring.
```

