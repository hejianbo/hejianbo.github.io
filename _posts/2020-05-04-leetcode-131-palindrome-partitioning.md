---
layout: post
title: 每日一题 - 131.Palindrome Partitioning
tags:
- leetcode
- 回文串
- 回溯
- 动态规划
- Medium
categories: leetcode
description: LeetCode 131.Palindrome Partitioning
---

# 131.Palindrome Partitioning

https://leetcode.com/problems/palindrome-partitioning/



## Description

Given a string *s*, partition *s* such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of *s*.

**Example:**

```
Input: "aab"
Output:
[
  ["aa","b"],
  ["a","a","b"]
]
```

## Solution 回溯

这个题一看到题就很显示感觉到可能需要用回溯来进行解题.

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> answer = new ArrayList<>();
        if (s == null || s.isEmpty()) {
            return answer;
        }
        
        // 回溯
        this.helper(answer, new ArrayList<>(), s, 0);
        
        return answer;
    }
    
    private void helper(List<List<String>> answer, List<String> list, String s, int start) {
        if (start >= s.length()) {
            answer.add(new ArrayList<>(list));
        }
        
        for (int i = start; i < s.length(); i++) {
            if (this.isPalindrome(s, start, i)) {
                list.add(s.substring(start, i + 1));
                // 递归
                this.helper(answer, list, s, i + 1);
                // 回溯
                list.remove(list.size() - 1);
            }
        }
    }
    
    private boolean isPalindrome(String s, int start, int end) {
        while (start <= end) {
            if (s.charAt(start) != s.charAt(end)) {
                return false;
            }
            start++;
            end--;
        }
        return true;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Palindrome Partitioning.
Memory Usage: 40.1 MB, less than 47.73% of Java online submissions for Palindrome Partitioning.
```

从执行结果上看, 还是不错的, 但这个明显和暴力没啥区别, 所以还是尝试一下优化吧.

### 优化: 回文串缓存

首先很显示可以看到, isPalindrome是可以不必每次都计算的, 所以我们可以直接缓存掉, 优化后如下

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> answer = new ArrayList<>();
        if (s == null || s.isEmpty()) {
            return answer;
        }
        
        // 缓存
        int[][] cache = new int[s.length()][s.length()];
        
        // 执行回溯访问
        this.helper(cache, answer, new ArrayList<>(), s, 0);
        
        return answer;
    }
    
    private void helper(int[][] cache, List<List<String>> answer, List<String> list, String s, int start) {
        // 终止条件: 整个字符串访问完成, 然后将其加到结果中
        if (start >= s.length()) {
            answer.add(new ArrayList<>(list));
        }
        
        for (int i = start; i < s.length(); i++) {            
            // 如果当前串是回文串, 则满足条件
            if (this.isPalindrome(cache, s, start, i)) {
                // 将回文子串加到当前这组答案list中
                list.add(s.substring(start, i + 1));
                // 从上面回文串后继续探索
                this.helper(cache, answer, list, s, i + 1);
                // 回溯
                list.remove(list.size() - 1);
            }
        }
    }
    // 重载这个方法, 先通过缓存获取, 获取不到再实际计算
    private boolean isPalindrome(int[][] cache, String s, int start, int end) {
        if (cache[start][end] > 0) {
            return cache[start][end] == 1;
        }
        // 1: 是回文串; 2: 不是回文串
        cache[start][end] = this.isPalindrome(s, start, end) ? 1 : 2;
        return cache[start][end] == 1;
    }
    
    private boolean isPalindrome(String s, int start, int end) {
        
        // 回文串的条件: 从头到尾与从尾到头看是一样的字符串
        while (start <= end) {
            if (s.charAt(start) != s.charAt(end)) {
                return false;
            }
            start++;
            end--;
        }
        return true;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Palindrome Partitioning.
Memory Usage: 41 MB, less than 18.18% of Java online submissions for Palindrome Partitioning.
```

### 优化: 回文串算法优化

首先看回文串的定义: 从前往后看与从后往前看是一样的字符串. 所以可以看出:

- 单个字符本身可以看成是一个字符串
- 两个字符必须相等
- 三个字符可以看成(3-1) / 2
- 四个字符可以看成(2个字符前后各加一个相等的字符)
- 五个字符可以看成(3个字符前后各加一个相等的字符)
- .............

总结:  当字符串n是回文串只需要看: 首尾 + 中间(n-2)个字符串是否相等.  

修改代码后如下: 

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> answer = new ArrayList<>();
        if (s == null || s.isEmpty()) {
            return answer;
        }
        
        // 缓存
        int[][] cache = new int[s.length()][s.length()];
        
        // 执行回溯访问
        this.helper(cache, answer, new ArrayList<>(), s, 0);
        
        return answer;
    }
    
    private void helper(int[][] cache, List<List<String>> answer, List<String> list, String s, int start) {
        // 终止条件: 整个字符串访问完成, 然后将其加到结果中
        if (start >= s.length()) {
            answer.add(new ArrayList<>(list));
        }
        
        for (int i = start; i < s.length(); i++) {            
            // 如果当前串是回文串, 则满足条件
            if (this.isPalindrome(cache, s, start, i)) {
                // 将回文子串加到当前这组答案list中
                list.add(s.substring(start, i + 1));
                // 从上面回文串后继续探索
                this.helper(cache, answer, list, s, i + 1);
                // 回溯
                list.remove(list.size() - 1);
            }
        }
    }
    // 重载这个方法, 先通过缓存获取, 获取不到再实际计算
    private boolean isPalindrome(int[][] cache, String s, int start, int end) {
        if (cache[start][end] > 0) {
            return cache[start][end] == 1;
        }
        // 1: 是回文串; 2: 不是回文串
        cache[start][end] = this.isPalindrome2(cache, s, start, end) ? 1 : 2;
        return cache[start][end] == 1;
    }
    
    private boolean isPalindrome2(int[][] cache, String s, int start, int end) {
        
        // 回文串的条件: 从头到尾与从尾到头看是一样的字符串
        while (start <= end) {
            if (s.charAt(start) != s.charAt(end)) {
                return false;
            }
            if (start + 1 <= end - 1 && cache[start + 1][end - 1] > 0) {
                return cache[start + 1][end - 1] == 1;
            }
            start++;
            end--;
        }
        return true;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Palindrome Partitioning.
Memory Usage: 40 MB, less than 68.18% of Java online submissions for Palindrome Partitioning.
```

### 优化：基于回文串算法

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> answer = new ArrayList<>();
        if (s == null || s.isEmpty()) {
            return answer;
        }
                
        boolean[][] cache = getCache(s);        
        // 执行回溯访问
        this.helper(cache, answer, new ArrayList<>(), s, 0);
        
        return answer;
    }
    
    private void helper(boolean[][] cache, List<List<String>> answer, List<String> list, String s, int start) {
        if (start >= s.length()) {
            answer.add(new ArrayList<>(list));
            return;
        }
               
        for (int i = start; i < s.length(); i++) {            
            if (cache[start][i]) {
                list.add(s.substring(start, i + 1));
                this.helper(cache, answer, list, s, i+1);
                list.remove(list.size() - 1);
            }            
        }
    }
    
    private boolean[][] getCache(String s) {
        int n = s.length();
        boolean[][] cache = new boolean[s.length()][s.length()];
        // 单个字符都是回文串
        for (int i = 0; i < n; i++) {
            cache[i][i] = true;
        }
        
        for (int step = 1; step < n; step++) {
            for (int i = step; i < n; i++) {
                int start = i - step;
                int end = i;
                // 回文串条件: 首尾相等, 并且子串也是回文串
                if (s.charAt(start) == s.charAt(end)) {
                    cache[start][end] = true;
                    if (step > 2 && !cache[start + 1][end - 1]) {
                        cache[start][end] = false;
                    }                    
                }
            }            
        }
        return cache;
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 100.00% of Java online submissions for Palindrome Partitioning.
Memory Usage: 40.4 MB, less than 34.09% of Java online submissions for Palindrome Partitioning.
```

感觉递归应该也是可以消掉的, 感觉那样就不容易理解了, 就这样吧.