---
layout: post
title: 每日一题 - LeetCode 72.Edit Distance
tags:
- leetcode
- 动态规划
categories: leetcode
description: LeetCode 72.Edit Distance
---

# 72.Edit Distance

https://leetcode.com/problems/edit-distance/

## Description

Given two words *word1* and *word2*, find the minimum number of operations required to convert *word1* to *word2*.

You have the following 3 operations permitted on a word:

1. Insert a character
2. Delete a character
3. Replace a character

**Example 1:**

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**

```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

## Solution 1 递归(Time Limit Executed)

```java
class Solution {
    public int minDistance(String word1, String word2) {
        return this.helper(word1, word2);
    }
    
    private int helper(String word1, String word2) {
        int n = word1.length();
        int m = word2.length();
        
        if (n == 0) {
            return m;
        }
        if (m == 0) {
            return n;
        }
        
        if (word1.charAt(n - 1) == word2.charAt(m - 1)) {
            return this.helper(word1.substring(0, n - 1), word2.substring(0, m - 1));
        }
        int insert = this.helper(word1, word2.substring(0, m - 1));
        int delete = this.helper(word1.substring(0, n - 1), word2);
        int replace = this.helper(word1.substring(0, n - 1), word2.substring(0, m - 1));
        
        return 1 + Math.min(insert, Math.min(delete, replace));
    }
}
```



## Solution 2 动态规划(递归)

观察**Solution1**或者画出递归树，可以发现上面很多重复的地方，所以可以首先加上缓存，消除这个重复。

```java
class Solution {
    public int minDistance(String word1, String word2) {        
        int[][] cache = new int[word1.length() + 1][word2.length() + 1];
        return this.helper(word1, word2, cache);
    }
    
    private int helper(String word1, String word2, int[][] cache) {
        int n = word1.length();
        int m = word2.length();
        
        if (n == 0) {
            return m;
        }
        if (m == 0) {
            return n;
        }
        // check cache
        if (cache[n][m] > 0) {
            return cache[n][m];
        }        
        
        if (word1.charAt(n - 1) == word2.charAt(m - 1)) {
            return this.helper(word1.substring(0, n - 1), word2.substring(0, m - 1), cache);
        }
        
        int insert = this.helper(word1, word2.substring(0, m - 1), cache);
        int delete = this.helper(word1.substring(0, n - 1), word2, cache);
        int replace = this.helper(word1.substring(0, n - 1), word2.substring(0, m - 1), cache);
        
        cache[n][m] = 1 + Math.min(insert, Math.min(delete, replace));                
        
        return cache[n][m];
    }
}
```

执行结果

```
Runtime: 27 ms, faster than 5.25% of Java online submissions for Edit Distance.
Memory Usage: 42 MB, less than 5.88% of Java online submissions for Edit Distance.
```

## Solution 3 动态规划(递推)

递推如果直接写，真不好写，还是递归的易理解，但递归的性能会差一些，如果需要写递推，建议把递归的树画出来，然后根据递归的树来得到推递式，从而写出递推结果.

![递归树](https://jianbo.me/assets/img/leetcode/20200416_72_edit_distance.png)

```java
class Solution {
    public int minDistance(String word1, String word2) {   
        int n = word1.length();
        int m = word2.length();
        
        int[][] dp = new int[n + 1][m + 1];
        for (int i = 0; i < n + 1; i++) {
            Arrays.fill(dp[i], Integer.MAX_VALUE);
            dp[i][0] = i;
        }
        for (int j = 0; j < m + 1; j++) {            
            dp[0][j] = j;
        }
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(dp[i][j - 1], Math.min(dp[i - 1][j], dp[i - 1][j - 1])) + 1;    
                }             
            }
        }
        
        return dp[n][m];
    }
}
```

执行结果

```
Runtime: 5 ms, faster than 77.53% of Java online submissions for Edit Distance.
Memory Usage: 39.7 MB, less than 5.88% of Java online submissions for Edit Distance.
```

