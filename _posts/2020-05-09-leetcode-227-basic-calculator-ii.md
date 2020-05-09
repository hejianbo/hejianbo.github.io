---
layout: post
title: 每日一题 - 227.Basic Calculator II 
tags:
- leetcode
- Medium
- 栈
categories: leetcode
description: LeetCode 227.Basic Calculator II 
---

# 227.Basic Calculator II 

https://leetcode.com/problems/basic-calculator-ii/

这是一道栈的典型应用的题: 中缀表达式转换为后缀表达式.

## Description

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only **non-negative** integers, `+`, `-`, `*`, `/` operators and empty spaces ``. The integer division should truncate toward zero.

**Example 1:**

```
Input: "3+2*2"
Output: 7
```

**Example 2:**

```
Input: " 3/2 "
Output: 1
```

**Example 3:**

```
Input: " 3+5 / 2 "
Output: 5
```

**Note:**

- You may assume that the given expression is always valid.
- **Do not** use the `eval` built-in library function.

## Solution 1

直接计算, 这个解不值得看, 思路和暴力没啥区别.

```java
class Solution {
    private List<Character> operators = Arrays.asList('+', '-', '*', '/');
    
    public int calculate(String s) {        
        Stack<String> stack = new Stack<>();        
        int i = 0;
        while (i < s.length()) {
            char c = s.charAt(i);
            if (c == ' ') {
                i++;
            } else if (operators.contains(c)) {
                stack.push(String.valueOf(c));
                i++;
            } else {                
                StringBuilder sb = new StringBuilder();
                sb.append(s.charAt(i++));
                while (i < s.length() && s.charAt(i) >= 48 && s.charAt(i) <= 57) {
                    sb.append(s.charAt(i++));
                }
                if (!stack.isEmpty() && (stack.peek().equals("*") || stack.peek().equals("/"))) {
                    String operator = stack.pop();
                    int left = Integer.valueOf(stack.pop());
                    int right  = Integer.valueOf(sb.toString());
                    if (operator.equals("*")) {
                        stack.push(String.valueOf(right * left));
                    } else{
                        stack.push(String.valueOf(left / right));
                    }
                } else {
                    stack.push(sb.toString());
                }
            }
        }
        int total = 0;
        while (!stack.isEmpty() && stack.size() > 1) {
            int right = Integer.valueOf(stack.pop());
            String operator = stack.pop();
            total = operator.equals("+") ? total + right : total - right;
        }
        
        return stack.isEmpty() ? total : total + Integer.valueOf(stack.pop());
    }
}
```



## Solution 2 后缀表达式

这种简单的计算通常都是转为后缀表达式再计算.  中缀表达式转换成后缀表达式步骤如下:

1. 如果遇到数字则直接入后缀表达式
2. 如果遇到操作符, 将操作符栈顶中不高于当前操作符优化级的全部入后缀表达式
3. 将当前读入的操作符入操作符栈
4. 持续上面步骤，将中缀表达式全部扫描完成, 然后将操作符栈中剩余的操作符入后缀表达式

```java
class Solution {
    private static List<Character> OPERATORS = Arrays.asList('+', '-', '*', '/');

    public int calculate(String s) {
        // 栈, 用来保存操作符
        Stack<String> stack = new Stack<>();
        // 后缀表达式
        List<String> list = new ArrayList<>();
        int i = 0;
        while (i < s.length()) {
            char c = s.charAt(i);
            // +-*/ 操作符
            if (OPERATORS.contains(c)) {
                // 将栈中优化级不比自己低的全部放入后缀表达式中, 然后自己入栈
                if (c == '+' || c == '-') {
                    // 所有运算符都不比+-优化级低, 所以全部要放入表达式中
                    while (!stack.isEmpty()) {
                        list.add(String.valueOf(stack.pop()));
                    }
                } else {
                    // 将栈顶的乘除法放入表达式中
                    while (!stack.isEmpty() && (stack.peek().equals("*") || stack.peek().equals("/"))) {
                        list.add(String.valueOf(stack.pop()));
                    }
                }
                stack.push(String.valueOf(c));
            } else if (c != ' '){ // 操作数
                StringBuilder sb = new StringBuilder();
                sb.append(c);
                while (i + 1 < s.length() && s.charAt(i + 1) >= 48 && s.charAt(i + 1) <= 57) {
                    i = i + 1;
                    sb.append(s.charAt(i));
                }
                list.add(sb.toString());
            }
            i++;
        }
        while (!stack.isEmpty()) {
            list.add(String.valueOf(stack.pop()));
        }

        i = 0;
        // 通过栈来计算后缀表达式
        while (i < list.size()) {
            char firstChar = list.get(i).charAt(0);
            if (OPERATORS.contains(firstChar)) {
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
                stack.push(String.valueOf(sum));
            } else {
                stack.push(list.get(i));
            }
            i++;
        }


        return Integer.valueOf(stack.pop());
    }
}
```

执行结果

```
Runtime: 37 ms, faster than 8.88% of Java online submissions for Basic Calculator II.
Memory Usage: 54.7 MB, less than 5.97% of Java online submissions for Basic Calculator II.

```

