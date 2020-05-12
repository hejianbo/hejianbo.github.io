---
layout: post
title: 每日一题 - 127. Word Ladder
tags:
- leetcode
- Medium
- 广度优化搜索
categories: leetcode
description: LeetCode 127. Word Ladder
---

# 127. Word Ladder

https://leetcode.com/problems/word-ladder/

## Description

Given two words (*beginWord* and *endWord*), and a dictionary's word list, find the length of shortest transformation sequence from *beginWord* to *endWord*, such that:

1. Only one letter can be changed at a time.
2. Each transformed word must exist in the word list.

**Note:**

- Return 0 if there is no such transformation sequence.
- All words have the same length.
- All words contain only lowercase alphabetic characters.
- You may assume no duplicates in the word list.
- You may assume *beginWord* and *endWord* are non-empty and are not the same.

**Example 1:**

```
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

**Example 2:**

```
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```

## Solution 1. 广度优化搜索

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 通过广度优化搜索
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        int times = 1;
        while (!queue.isEmpty()) {
            // 广度优化搜索的一次展开
            int length = queue.size();            
            for (int j = 0; j < length; j++) {
                String source = queue.poll();
                Iterator<String> iter = wordList.iterator();
                while (iter.hasNext()) {
                    String target = iter.next();
                    // 通过从source转换到target
                    if (this.canTransform(source, target)) {
                        if (target.equals(endWord)) {
                            return times + 1;
                        }
                        queue.offer(target);
                        iter.remove();
                    }                    
                }
            }
            // 每展开一次, 表示进行了一次转换, 所以次数加1
            times++;
        }
        
        return 0;
    }
    
    
    private boolean canTransform(String source, String target){
        // 不相等字符数, 只有一个不相等表示能够进行转换
        int notEqualChars = 0;
        for (int i = 0; i < source.length();i++) {
            if (source.charAt(i) != target.charAt(i)) {
                notEqualChars++;
            }
        }
        return notEqualChars == 1;
    }
}
```

执行结果

```
Runtime: 536 ms, faster than 20.61% of Java online submissions for Word Ladder.
Memory Usage: 39.7 MB, less than 86.86% of Java online submissions for Word Ladder.
```

