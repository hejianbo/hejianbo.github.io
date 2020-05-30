---
layout: post
title: 每日一题 - 30. Substring with Concatenation of All Words
tags:
- leetcode
- Hard
categories: leetcode
description: LeetCode 30. Substring with Concatenation of All Words
---

# 30. Substring with Concatenation of All Words

https://leetcode.com/problems/substring-with-concatenation-of-all-words/



## Description

You are given a string, **s**, and a list of words, **words**, that are all of the same length. Find all starting indices of substring(s) in **s** that is a concatenation of each word in **words** exactly once and without any intervening characters.

 

**Example 1:**

```
Input:
  s = "barfoothefoobarman",
  words = ["foo","bar"]
Output: [0,9]
Explanation: Substrings starting at index 0 and 9 are "barfoo" and "foobar" respectively.
The output order does not matter, returning [9,0] is fine too.
```

**Example 2:**

```
Input:
  s = "wordgoodgoodgoodbestword",
  words = ["word","good","best","word"]
Output: []
```



## Solution

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> answer = new ArrayList<>();
        if (s == null || s.isEmpty() || words == null || words.length == 0) {
            return answer;
        }
        
        int size = words.length;
        int len = words[0].length();
        
        Map<String, Integer> wordMap = new HashMap<>();
        for (String word : words) {
            wordMap.put(word, wordMap.getOrDefault(word, 0) + 1);
        }
        
        int lens = size * len;
        
        for (int i = 0; i <= s.length() - lens; i++) {
            Map<String, Integer> subMap = new HashMap<>();
            boolean isMatch = true;
            for (int j = i; j < i + lens; j+= len) {
                String subWord = s.substring(j, j + len);
                // 如果不在目标单词中，则表示此次匹配失败
                if (!wordMap.containsKey(subWord)) {
                    isMatch = false;
                    break;
                }
                // 找到的单词数量不一致，也不满足结果
                subMap.put(subWord, subMap.getOrDefault(subWord, 0) + 1);
                if (subMap.get(subWord) > wordMap.get(subWord)) {
                    isMatch = false;
                    break;
                }
            }
            if (isMatch) {
                answer.add(i);
            }
        }
        
        return answer;
        
    }
}
```

