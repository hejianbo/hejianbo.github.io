---
layout: post
title: 每日一题 - 44.Wildcard Matching
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 44.Wildcard Matching
---

# 44.Wildcard Matching

https://leetcode.com/problems/wildcard-matching/

这题和另外一题比较像. 

[10.Regular Expression Matching](https://jianbo.me/leetcode/2020/04/23/leetcode-10-regular-expression-matching/)

## Description

Given an input string (`s`) and a pattern (`p`), implement wildcard pattern matching with support for `'?'`and `'*'`.

```
'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).
```

The matching should cover the **entire** input string (not partial).

**Note:**

- `s` could be empty and contains only lowercase letters `a-z`.
- `p` could be empty and contains only lowercase letters `a-z`, and characters like `?` or `*`.

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
p = "*"
Output: true
Explanation: '*' matches any sequence.
```

**Example 3:**

```
Input:
s = "cb"
p = "?a"
Output: false
Explanation: '?' matches 'c', but the second letter is 'a', which does not match 'b'.
```

**Example 4:**

```
Input:
s = "adceb"
p = "*a*b"
Output: true
Explanation: The first '*' matches the empty sequence, while the second '*' matches the substring "dce".
```

**Example 5:**

```
Input:
s = "acdcb"
p = "a*c?b"
Output: false
```



## Solution 1. 递归

这题边界条件有点多, 很容易出错.

```java
class Solution {
    public boolean isMatch(String s, String p) {
        // 将连续的*号给转换成单个*号, 因为多个*号与单个*号是同样的效果
        StringBuilder sb = new StringBuilder();
        for (Character c : p.toCharArray()) {
            if (c == '*' && sb.length() > 0 && sb.charAt(sb.length() - 1) == '*') {
                continue;
            }
            sb.append(c);
        }
        
        int[][] cache = new int[s.length()][p.length()];
        
        return this.isMatch(cache, s, sb.toString(), 0, 0);
    }
    
    private boolean isMatch(int[][] cache, String s, String pattern, int sIndex, int pIndex) {        
        if (sIndex == s.length() && pIndex == pattern.length()) {
            return true;
        }
              
        if (sIndex == s.length() && pattern.substring(pIndex).equals("*")) {
            return true;
        }
        if (sIndex == s.length()) {
            return false;
        }
        if (pIndex == pattern.length()) {
            return false;
        }
        
        if (cache[sIndex][pIndex] > 1) {
            return cache[sIndex][pIndex] == 1;
        }        
        
        char pc = pattern.charAt(pIndex);     
        
        // 如果模式是普通字符, 如果字符不相等, 表示
        boolean result = false;
        if (pc != '?' && pc!= '*') {           
            if(s.charAt(sIndex) == pattern.charAt(pIndex)) {
                result = this.isMatch(cache, s, pattern, sIndex + 1, pIndex + 1);
            }
        } else if (pc == '?') {
            result = this.isMatch(cache, s, pattern, sIndex + 1, pIndex + 1); // 匹配一个字符
        }else {
            result = this.isMatch(cache, s, pattern, sIndex, pIndex + 1) 
                        || this.isMatch(cache, s, pattern, sIndex + 1, pIndex);
        }
        cache[sIndex][pIndex] = result ? 1 : 2;
        return result;
    }
}
```

执行结果

```
Runtime: 18 ms, faster than 70.31% of Java online submissions for Wildcard Matching.
Memory Usage: 43.7 MB, less than 6.98% of Java online submissions for Wildcard Matching.
```

