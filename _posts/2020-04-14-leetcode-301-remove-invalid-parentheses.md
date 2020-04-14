---
layout: post
title: 每日一题 - LeetCode 301.Remove Invalid Parentheses
tags:
- leetcode
- 回溯
categories: leetcode
description: LeetCode 301.Remove Invalid Parentheses
---

# 301.Remove Invalid Parentheses

## Description

Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

**Note:** The input string may contain letters other than the parentheses `(` and `)`.

**Example 1:**

```
Input: "()())()"
Output: ["()()()", "(())()"]
```

**Example 2:**

```
Input: "(a)())()"
Output: ["(a)()()", "(a())()"]
```

**Example 3:**

```
Input: ")("
Output: [""]
```

## Solution 1 回溯法

```java
class Solution {        
    public List<String> removeInvalidParentheses(String s) {
        List<String> list = new ArrayList<>();
        if (s == null) {
            return null;
        }
        
        this.helper(new StringBuilder(s), 0, list);
        
        return list;        
    }
    
    private void helper(StringBuilder sb, int index, List<String> list) {
        if (sb.length() == 0 && list.size() == 0) {
            list.add("");
            return;
        }        
        if (!list.isEmpty() && sb.length() < list.get(0).length()) {
            return;
        }
        
        String current = sb.toString();
        if (this.isValid(current)) {            
            if (!list.isEmpty() && list.get(0).length() < current.length()) {
                list.clear();
            }            
            this.appendToList(list, current);
            return;
        }
        
        if (index > sb.length() - 1) {
            return;
        }
        char c = sb.charAt(index);
        if (c == ')' || c == '(') {
            // 删除该字符            
            sb.deleteCharAt(index);
            this.helper(sb, index, list);

            // 回溯
            sb.insert(index, c);
            this.helper(sb, index + 1, list);
        } else {
            this.helper(sb, index + 1, list);
        }
    }
    
    private boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push('(');
            } else if (c == ')') {
                if (!stack.isEmpty()) {
                    stack.pop();
                } else {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }
    
    private void appendToList(List<String> list, String str) {       
        
        for (String item : list) {            
            if (str.equals(item)) {
                return;
            }
        }
        list.add(str);
    }
}
```

执行结果

```
Runtime: 493 ms, faster than 5.01% of Java online submissions for Remove Invalid Parentheses.
Memory Usage: 40.1 MB, less than 80.43% of Java online submissions for Remove Invalid Parentheses.
```

