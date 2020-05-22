---
layout: post
title: 每日一题 - 140. Word Break II
tags:
- leetcode
- Hard
- Trie
- 深度优化搜索
- 动态规划
categories: leetcode
description: LeetCode 140. Word Break II
---

# 140. Word Break II

https://leetcode.com/problems/word-break-ii/

## Description

Given a **non-empty** string *s* and a dictionary *wordDict* containing a list of **non-empty**words, add spaces in *s* to construct a sentence where each word is a valid dictionary word. Return all such possible sentences.

**Note:**

- The same word in the dictionary may be reused multiple times in the segmentation.
- You may assume the dictionary does not contain duplicate words.

**Example 1:**

```
Input:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
Output:
[
  "cats and dog",
  "cat sand dog"
]
```

**Example 2:**

```
Input:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
Output:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
Explanation: Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```
Input:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
Output:
[]
```

## Solution 1. 基于Trie的DFS  

这个解主要是根据上一题　[Word Search II　](https://jianbo.me/leetcode/2020/05/21/leetcode-212-word-search-ii/)　得到的.

```java
class Solution {
    // 答案
    private List<String> answer = new ArrayList<>();
    // TrieTree用于单词的搜索
    private TrieTree tree = new TrieTree();
    
    public List<String> wordBreak(String s, List<String> wordDict) {        
        if (s == null || s.isEmpty() || wordDict == null || wordDict.isEmpty()) {
            return answer;
        }
        // 这里必须先检测是否有解, 不然会超时
        Set<String> set = new HashSet<>(wordDict);
        boolean[] isValid  = new boolean[s.length()+1];
        isValid[0] = true;        
        for(int i = 1; i <= s.length(); i++) {  
            for(int j = 0; j < i; j++) {                
                if(isValid[j] && set.contains(s.substring(j,i))) {                    
                    isValid[i] = true;
                    break;
                }                
            }
        }          
        if(!isValid[s.length()]) {            
            return new ArrayList<String>();
        }   
                        
        // 构造一棵Trie树        
        this.tree.build(wordDict);
        
        // 执行深度优化搜索
        this.dfs(new ArrayList<String>(), tree.root, s, 0);    
        
        return this.answer;
    }
    
    private void dfs(List<String> sentence, TrieNode node, String s, int start) {
        // 处理完了所有字符, 表示找到了一个句子
        if (start >= s.length()) {
            if (node == this.tree.root) {
                this.answer.add(String.join(" ", sentence));
            }
            return;
        }
        char c = s.charAt(start);
        // 如果当前Trie结点下没有这个字符, 表示这样搜索是找不到解的
        if (!node.children.containsKey(c)) {
            return;
        }
        // 如果能找到, 则有两种情况, 是单词或不是单词
        TrieNode child = node.children.get(c);
        // 如果是单词, 多一种处理情况
        if (child.word != null) {
            sentence.add(child.word);         
            this.dfs(sentence, tree.root, s, start + 1);
            // 回溯
            sentence.remove(sentence.size() - 1);
        }
        this.dfs(sentence, child, s, start + 1);        
    }
}

class TrieNode {
    String word;
    Map<Character, TrieNode> children;
    
    TrieNode() {
        this.children = new HashMap<>();
    }
}

class TrieTree {
    TrieNode root;
    TrieTree() {
        this.root = new TrieNode();
    }
    
    void build(List<String> wordDict) {
        for (int i = 0; i < wordDict.size(); i++) {
            TrieNode p = this.root;
            String word = wordDict.get(i);
            for (int j = 0; j < word.length(); j++) {
                Character c = word.charAt(j);
                if (!p.children.containsKey(c)) {
                    p.children.put(c, new TrieNode());
                }
                p = p.children.get(c);
            }
            p.word = word;
        }
    }
}
```

执行结果

```
Runtime: 8 ms, faster than 77.15% of Java online submissions for Word Break II.
Memory Usage: 41.1 MB, less than 6.56% of Java online submissions for Word Break II.
```



## Solution 2 动态规划

```java
class Solution {

    public List<String> wordBreak(String s, List<String> wordDict) {
        if (s == null || s.isEmpty() || wordDict == null || wordDict.isEmpty()) {
            return new ArrayList<>();
        }
        
        Set<String> set = new HashSet<>();
        int maxLength = 0;
        for (String word : wordDict) {
            set.add(word);
            maxLength = Math.max(maxLength, word.length());
        }
        
        boolean[] isValid  = new boolean[s.length()+1];
        isValid[0] = true;        
        for(int i = 1; i <= s.length(); i++) {            
            for(int j=0; j < i; j++) {                
                if(isValid[j] && set.contains(s.substring(j,i))) {                    
                    isValid[i] = true;
                    break;
                }
            }
        }        
        if(!isValid[s.length()]) {            
            return new ArrayList<String>();
        }
        
        // 缓存
        Map<Integer, List<String>> cache = new HashMap<>();
        cache.put(0, Arrays.asList(""));            
        // 递推, 从字符串长度为１开始, 逐渐展开到n;
        // 每展开一个长度(n + 1), 新展开的结果都会依赖前面的结果[i, n]
        // 只要从cache[i..n] + string[i, n + 1)满足条件, 都是正确的解
        for (int i = 1; i <= s.length(); i++) {
            List<String> list = new ArrayList<>();
            // 因为正确的单词的最大长度为maxLength
            // 所以只需要往前回溯maxLength长度的位置即可
            int possibleStart = Math.max(i - maxLength, 0);
            for (int j = possibleStart; j < i; j++) {
                // 子串[j, i)
                String substring = s.substring(j, i);
                // 只有cache[j]是正确的解的情况下, 再看后面的子串是否满足条件
                if (cache.containsKey(j) && set.contains(substring)) {
                    for (String prefix : cache.get(j)) {
                        list.add(prefix + (prefix.length() > 0 ? " " : "") + substring);
                    }
                }
            }
            if (!list.isEmpty()) {
                cache.put(i, list);
            }
        }
        

        return cache.getOrDefault(s.length(), new ArrayList<>());
    }
}
```

执行结果

```
Runtime: 15 ms, faster than 28.44% of Java online submissions for Word Break II.
Memory Usage: 39.9 MB, less than 16.40% of Java online submissions for Word Break II.
```

