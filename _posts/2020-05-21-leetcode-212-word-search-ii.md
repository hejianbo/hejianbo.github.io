---
layout: post
title: 每日一题 - 212.Word Search II
tags:
- leetcode
- Hard
- Trie
- 深度优化搜索
categories: leetcode
description: LeetCode 212.Word Search II
---

# 212.Word Search II

https://leetcode.com/problems/word-search-ii/



## Description

Given a 2D board and a list of words from the dictionary, find all words in the board.

Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

 

**Example:**

```
Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]
```

 

**Note:**

1. All inputs are consist of lowercase letters `a-z`.
2. The values of `words` are distinct.



## Solution 1.深度优化搜索 

这个解比较直观, 就是根据首字母进行深度优化搜索遍历．

```java
class Solution {
    public List<String> findWords(char[][] board, String[] words) {        
        List<String> answer = new ArrayList<>();
        if (board == null || board.length == 0) {
            return answer;
        }
        int rows = board.length;
        int cols = board[0].length;
        Set<String> wordSet = Arrays.stream(words).collect(Collectors.toSet());
                
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                Iterator<String> wordIterator = wordSet.iterator();
                while (wordIterator.hasNext()) {
                    String word = wordIterator.next();
                    if (word.charAt(0) == board[row][col]) {
                        // cache也可以不用, 这样就需要修改board的字符作为哨兵
                        boolean[][] cache = new boolean[rows][cols];
                        if (this.dfs(cache, board, word, row, col, 0)) {
                            answer.add(word);
                            wordIterator.remove();
                        }
                    }
                }
            }
        }
        
        return answer;
    }
    
    private boolean dfs(boolean[][] cache, char[][] board, String word, int row, int col, int start) {        
        // 不匹配
        if (board[row][col] != word.charAt(start)) {
            return false;
        } 
        // 如果已经完成了匹配, 则结束
        if (start == word.length() - 1) {
            return true;
        }
        
   		// 标记该字母已经被访问过了, 避免重复访问　
        cache[row][col] = true;
        // 上下左右, 四个方向进行遍历
        if (row - 1 >= 0 && !cache[row - 1][col] && this.dfs(cache, board, word, row - 1, col, start + 1)) {
            return true;
        }
        if (row + 1 < board.length && !cache[row + 1][col] && this.dfs(cache, board, word, row + 1, col, start + 1)) {
            return true;
        }
        if (col - 1 >= 0 && !cache[row][col - 1] && this.dfs(cache, board, word, row, col - 1, start + 1)) {
            return true;
        }
        if (col + 1 < board[0].length && !cache[row][col + 1] && this.dfs(cache, board, word, row, col + 1, start + 1)) {
            return true;
        }        
        // 回溯
        cache[row][col] = false;        
        return false;
    }
}
```

执行结果

```
Runtime: 291 ms, faster than 27.50% of Java online submissions for Word Search II.
Memory Usage: 42.8 MB, less than 80.00% of Java online submissions for Word Search II.
```

## Solution 2. Trie + DFS

上面的搜索中, 每一次DFS都是独立的, 而通过Trie, 在出现有公共前缀的word的时候, 可以不停止, 则是继续搜索下去.

```java
class Solution {
    public List<String> findWords(char[][] board, String[] words) {        
        List<String> answer = new ArrayList<>();
        if (board == null || board.length == 0) {
            return answer;
        }
        int rows = board.length;
        int cols = board[0].length;
        
        // 构建Trie树
        TrieTree tree = new TrieTree();
        tree.build(words);
                
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {                
                tree.dfs(answer, board, row, col, tree.root);                                
            }
        }
        
        return answer;
    }
        
    
    static class TrieNode {
        TrieNode[] children;
        // 如果这结果是一个完整的单词, 则存储起来
        String word;
        TrieNode() {
            this.children = new TrieNode[26];
        }
    }
    
    static class TrieTree {
        TrieNode root;
        TrieTree() {
            this.root = new TrieNode();
        }
        
        // 构建Trie树
        void build(String[] words) {            
            for (String word : words) {
                TrieNode p = this.root;
                for (int i = 0; i < word.length(); i++) {
                    int index = word.charAt(i) - 'a';
                    if (p.children[index] == null) {
                        p.children[index] = new TrieNode();
                        
                    } 
                    p = p.children[index];
                }
                //　标识搜索到的该结点为一个完整的单词
                p.word = word;
            }
        }
        
        void dfs(List<String> answer, char[][] board, int row, int col, TrieNode node) {
            if (row < 0 || row >= board.length || col < 0 || col >= board[0].length || board[row][col] == '#') {
                return;
            }
            char c = board[row][col];
            int index = c - 'a';
            // 如果在Trie中找不到, 则返回
            if (node.children[index] == null) {
                return;
            }
            // 如果找到了一个单词, 则加入结果中
            if (node.children[index].word != null) {
                answer.add(node.children[index].word);
                // 清除掉, 避免重复添加
                node.children[index].word = null;
            }
            board[row][col] = '#';
            // 继续搜索上下左右４个方向
            this.dfs(answer, board, row - 1, col, node.children[index]);
            this.dfs(answer, board, row + 1, col, node.children[index]);
            this.dfs(answer, board, row, col - 1, node.children[index]);
            this.dfs(answer, board, row, col + 1, node.children[index]);
            // 重置回去
            board[row][col] = c;
        }
    }
    
}
```

执行结果

```
Runtime: 9 ms, faster than 88.43% of Java online submissions for Word Search II.
Memory Usage: 48.7 MB, less than 20.00% of Java online submissions for Word Search II.
```

