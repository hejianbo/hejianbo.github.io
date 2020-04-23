---
layout: post
title: 每日一题 - 10.Regular Expression Matching
tags:
- leetcode
categories: leetcode
description: LeetCode 10.Regular Expression Matching
---

# 10.Regular Expression Matching

https://leetcode.com/problems/regular-expression-matching/

Given an input string (`s`) and a pattern (`p`), implement regular expression matching with support for `'.'` and `'*'`.

```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```

The matching should cover the **entire** input string (not partial).

**Note:**

- `s` could be empty and contains only lowercase letters `a-z`.
- `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`.

**Example 1:**

```
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**

```
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

**Example 3:**

```
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

**Example 4:**

```
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore, it matches "aab".
```

**Example 5:**

```
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false
```

## Solution 1 递归

```java
class Solution {
    public boolean isMatch(String s, String p) {
        if (p.isEmpty()) {
            return s.isEmpty();
        }
        
        return this.helper(s, 0, p, 0);
    }
    
    private boolean helper(String s, int start, String p, int pStart) {              
        int n = s.length();
        int m = p.length();          
        if (pStart == m) {
            return start == n;
        }
        // 如果第一个字符是否相等
        boolean firstMatch = false;
        if (start < n 
            && (s.charAt(start) == p.charAt(pStart) || p.charAt(pStart) == '.' )) {
            firstMatch = true;
        }
        // 模式的下一个字符为*的情况
        if (pStart + 1 < m && p.charAt(pStart + 1) == '*') {        
            // *匹配与不匹配(模式跳到*后)两种情况
            return this.helper(s, start, p, pStart + 2) // 不匹配, 则跳过模式
                // 匹配, 则跳过s一个字符继续下一个匹配
                || firstMatch && this.helper(s, start + 1, p, pStart); 
        } else {
            // 下一个字符不为*, 如果第一个字符匹配成功, 则直接进行一下个字符的匹配
            return firstMatch && this.helper(s, start + 1, p, pStart + 1);        
        }
    }
}
```

执行结果

```java
Runtime: 27 ms, faster than 32.49% of Java online submissions for Regular Expression Matching.
Memory Usage: 37.9 MB, less than 47.47% of Java online submissions for Regular Expression Matching.
```

优化一下, 消除重复, 加个缓存.

```java
class Solution {
    public boolean isMatch(String s, String p) {
        if (p.isEmpty()) {
            return s.isEmpty();
        }
        int[][] cache = new int[s.length() + 1][p.length() + 1];
        return this.helper(s, 0, p, 0, cache);
    }
    
    private boolean helper(String s, int start, String p, int pStart, int[][] cache) {              
        int n = s.length();
        int m = p.length();          
        if (pStart == m) {
            return start == n;
        }
        if (cache[start][pStart] > 0) {
            return cache[start][pStart] == 1;
        }
        // 如果第一个字符是否相等
        boolean firstMatch = false;
        if (start < n 
            && (s.charAt(start) == p.charAt(pStart) || p.charAt(pStart) == '.' )) {
            firstMatch = true;
        }
        // 模式的下一个字符为*的情况
        if (pStart + 1 < m && p.charAt(pStart + 1) == '*') {        
            // *匹配与不匹配(模式跳到*后)两种情况
            cache[start][pStart] = (this.helper(s, start, p, pStart + 2, cache) // 不匹配, 则跳过模式
                // 匹配, 则跳过s一个字符继续下一个匹配
                || firstMatch && this.helper(s, start + 1, p, pStart, cache)) ? 1 : 2; 
        } else {
            // 下一个字符不为*, 如果第一个字符匹配成功, 则直接进行一下个字符的匹配
            cache[start][pStart] =  firstMatch && this.helper(s, start + 1, p, pStart + 1, cache) ? 1 : 2;        
        }
        return cache[start][pStart] == 1;
    }
}
```



执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Regular Expression Matching.
Memory Usage: 39.7 MB, less than 37.37% of Java online submissions for Regular Expression Matching.
```