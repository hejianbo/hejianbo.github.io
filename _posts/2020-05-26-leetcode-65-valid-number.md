---
layout: post
title: 每日一题 - 65.Valid Number
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 65.Valid Number
---

# 65.Valid Number

https://leetcode.com/problems/valid-number/



## Description

Validate if a given string can be interpreted as a decimal number.

Some examples:
`"0"` => `true`
`" 0.1 "` => `true`
`"abc"` => `false`
`"1 a"` => `false`
`"2e10"` => `true`
`" -90e3   "` => `true`
`" 1e"` => `false`
`"e3"` => `false`
`" 6e-1"` => `true`
`" 99e2.5 "` => `false`
`"53.5e93"` => `true`
`" --6 "` => `false`
`"-+3"` => `false`
`"95a54e53"` => `false`

**Note:** It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one. However, here is a list of characters that can be in a valid decimal number:

- Numbers 0-9
- Exponent - "e"
- Positive/negative sign - "+"/"-"
- Decimal point - "."

Of course, the context of these characters also matters in the input.

**Update (2015-02-10):**
The signature of the `C++` function had been updated. If you still see your function signature accepts a `const char *` argument, please click the reload button to reset your code definition.



## Solution 

最讨论这种恶心的题了...................

```java
class Solution {
    public boolean isNumber(String s) {
        if (s == null || s.isEmpty()) {
            return false;
        }
        s = s.trim();      
        
        int signs = 0;
        int points = 0;
        int exponents = 0;
        int numbers = 0;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '+' || c == '-') {
                if (i > 0 && s.charAt(i - 1) == 'e') {
                    if (i == s.length() - 1) {
                        return false;
                    }
                } else {
                    signs++;
                }
            }else if (c == '.') {
                points++;
            } else if (c == 'e') {
                if (numbers == 0) {
                    return false;
                }
                exponents++;
            }else if (c >= 48 && c <= 57) {
                numbers++;
            } else {
                return false;
            }
        }
        // 符号数量不正确
        if (signs > 1 || points > 1 || exponents > 1 || numbers == 0) {
            return false;
        }
        // 正符号必须在起始位置 
        if (signs > 0 && (s.charAt(0) != '+' && s.charAt(0) != '-')) {
            return false;
        }        
        
        // 指数符号必须在小数点后面
        if (points > 0 && exponents > 0 && s.indexOf('.') > s.indexOf('e')) {
            return false;
        }

        // 指数符号不能在第一个或最后一个位置
        if (exponents > 0 && (s.charAt(0) == 'e' || s.charAt(s.length() - 1) == 'e')) {
            return false;
        }
        
        return true;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Valid Number.
Memory Usage: 39.9 MB, less than 6.25% of Java online submissions for Valid Number.
```

