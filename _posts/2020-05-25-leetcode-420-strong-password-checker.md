---
layout: post
title: 每日一题 - 420.Strong Password Checker
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 420.Strong Password Checker
---

# 420.Strong Password Checker

https://leetcode.com/problems/strong-password-checker/

## Description

A password is considered strong if below conditions are all met:

1. It has at least 6 characters and at most 20 characters.
2. It must contain at least one lowercase letter, at least one uppercase letter, and at least one digit.
3. It must NOT contain three repeating characters in a row ("...aaa..." is weak, but "...aa...a..." is strong, assuming other conditions are met).

Write a function strongPasswordChecker(s), that takes a string s as input, and return the **MINIMUM**change required to make s a strong password. If s is already strong, return 0.

Insertion, deletion or replace of any one character are all considered as one change.



## Solution

这道题关键就是删除字符时的操作, 因为删除重复数量不同的串时, 最后的结果是不一样的.

- 如果字符长度为3的倍数时,  删除操作再结合其它操作次数是不变的
- 其它情况, 删除操作都会产生更多的次数, 尤其是模为2的情况, 次数完全不一样, 所以这个优先级是最低的.
- 所以最终, 删除操作的优先级就是对3取模的升序进行处理

```java
class Solution {
    public int strongPasswordChecker(String s) {
        int n = s.length();
                            
        
        int typeCount = 3;
        boolean hasLower = false;
        boolean hasUpper = false;
        boolean hasDigit = false;              
                
        // 先检测大小写字母、数字是否满足
        // 保存每个字符重复的次数
        int[] repeated = new int[n];
        Arrays.fill(repeated, 1);
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);                    
            if (!hasLower && this.isLower(c)) {
                hasLower = true;
                typeCount--;
            }
            if (!hasUpper && this.isUpper(c)) {
                hasUpper = true;
                typeCount--;
            }            
            if (!hasDigit && this.isDigit(c)) {
                hasDigit = true;
                typeCount--;
            }
            if (i > 0) {
                char prevChar = s.charAt(i - 1);            
                if (c == prevChar) {
                    repeated[i] += repeated[i - 1];
                }
            }
        }
        // 找到重复3次及以上的字符, 并先处理重复次数最少的
        // 因为如果需要删除的时候, 删除长3个字符与长4个字符,是不一样的, 
        List<Integer> repeatList = new ArrayList<>();
        int index = n - 1;
        while (index >= 0) {
            if (repeated[index] >= 3) {
                repeatList.add(repeated[index]);
                index = index - repeated[index];
            } else {
                index--;
            }
        }
        
        // 需要增加的字符数量
        int needAddCount = Math.max(6 - n, 0);
        int addedCount = 0;
        // 需要删除的数量
        int needDelCount = Math.max(n - 20, 0);      
        int deletedCount = 0;
        int changeCount = 0;
        
        // 先处理删除的情况, 删除的比较特殊
        // 需要优化优先处理%3=0的情况, 因为这种情况处理了后, 后面再change不会增加修改次数
        // 如: 6 修改2次 = 删除1次再修改1次, 总次数是不变的
        //     5 修改1次 = 删除3次 = 删除1次还要修改1次 = 删除2次还要修改1次
        //     4 修改1次 = 删除2次 == 删除1次修改1次   = 删除2次
        // 所以需要优化将3的倍数的先处理次一次删除, 
        while (deletedCount < needDelCount && repeatList.size() > 0) {                        		// 先删模为0的
            index = -1;
            for (int i = 0; i < repeatList.size(); i++) {
                int number = repeatList.get(i);
                if (number % 3 == 0) {
                    index = i;
                    break;
                }
            }
            if (index >= 0) {
                int number = repeatList.get(index);
                repeatList.remove(index);
                number--;
                deletedCount++;
                if (number >= 3) {
                    repeatList.add(number);
                }
                continue;
            } 
            // 再处理模为1的
            for (int i = 0; i < repeatList.size(); i++) {
                int number = repeatList.get(i);
                if (number % 3 == 1) {
                    index = i;
                    break;
                }
            }
            if (index >= 0) {
                int number = repeatList.get(index);
                repeatList.remove(index);
                number--;
                deletedCount++;
                if (number >= 3) {
                    repeatList.add(number);
                }
                continue;
            } 
        	// 最后处理模为2的, 因为这种产生的次数可能最多
            for (int i = 0; i < repeatList.size(); i++) {
                int number = repeatList.get(i);
                if (number % 3 == 2) {
                    index = i;
                    break;
                }
            }
            if (index >= 0) {
                int number = repeatList.get(index);
                repeatList.remove(index);
                number--;
                deletedCount++;
                if (number >= 3) {
                    repeatList.add(number);
                }
                continue;
            } 
        }
        
        // 处理需要插入或者修改的情况 
        for (int number : repeatList) {                  
            while (number >= 3) {
                if (addedCount < needAddCount) {
                    number -= 2;
                    addedCount += 1;
                    typeCount--;
                } else {
                    number -= 3;
                    changeCount += 1;
                    typeCount--;
                }
            }
        }        
                
        if (typeCount > 0 && addedCount < needAddCount) {
            typeCount -= needAddCount - addedCount;
        }
        
        return  needAddCount // 添加的数量
                + needDelCount // 删除的数量
                + Math.max(typeCount, 0) // 类型改变的数量
                + changeCount;   // 需要修改的数量
    }
    
    private boolean isDigit(char c) {
        return c >= 48 && c <= 57;
    }
    
    private boolean isLower(char c) {
        return c >=97 && c <= 122;
    }
    
    private boolean isUpper(char c) {
        return c >= 65 && c <= 90;
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Strong Password Checker.
Memory Usage: 37 MB, less than 100.00% of Java online submissions for Strong Password Checker.

```

