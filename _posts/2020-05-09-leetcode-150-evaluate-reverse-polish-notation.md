---
layout: post
title: 每日一题 - 150.Evaluate Reverse Polish Notation
tags:
- leetcode
- Medium
- 栈
categories: leetcode
description: LeetCode 150.Evaluate Reverse Polish Notation
---

# 150.Evaluate Reverse Polish Notation

https://leetcode.com/problems/evaluate-reverse-polish-notation/

这题又是一题利用栈计算后缀表达式四则运算的题，另可以看一下这一题，这一题还说明了怎样将中缀表达式转换为后缀表达式，所以相对来说，此题更加简单. [Basic Calculator II](https://jianbo.me/leetcode/2020/05/09/leetcode-227-basic-calculator-ii/#227basic-calculator-ii)

## Description

Evaluate the value of an arithmetic expression in [Reverse Polish Notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

Valid operators are `+`, `-`, `*`, `/`. Each operand may be an integer or another expression.

**Note:**

- Division between two integers should truncate toward zero.
- The given RPN expression is always valid. That means the expression would always evaluate to a result and there won't be any divide by zero operation.

**Example 1:**

```
Input: ["2", "1", "+", "3", "*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

**Example 2:**

```
Input: ["4", "13", "5", "/", "+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

**Example 3:**

```
Input: ["10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"]
Output: 22
Explanation: 
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```



## Solution 使用栈计算后缀表达式

通过观察即可发现，题目中给出的是计算后的后缀表达式，所以这此题应该是极其简单了，因为不再需要自己去进行转换。这题和上一题基本差不多，并且更加简单

```java
class Solution {
    private static List<Character> OPERATORS = Arrays.asList('+', '-', '*', '/');
    
    public int evalRPN(String[] tokens) {
        // 栈, 用来保存操作数
        Stack<Integer> stack = new Stack<>();        
        for (int i = 0; i < tokens.length; i++) {
            char firstChar = tokens[i].charAt(0);
            // 因为数字存在负数, 所以在判断操作符时, 需要判断一下长度，确定一定是一个操作符
            if (tokens[i].length() == 1 && OPERATORS.contains(firstChar)) {
                int right = Integer.valueOf(stack.pop());
                int left = Integer.valueOf(stack.pop());  
                int sum = 0;
                switch(firstChar) {
                    case '+':
                        sum = left + right;
                        break;
                    case '-':
                        sum = left - right;
                        break;
                    case '*':
                        sum = left * right;
                        break;
                    case '/':
                        sum = left / right;
                        break;
                    default:
                        break;
                }
                stack.push(sum);
            } else {
                // 操作数入栈
                stack.push(Integer.valueOf(tokens[i]));
            }
        }
        
        return stack.pop();
    }
}
```

