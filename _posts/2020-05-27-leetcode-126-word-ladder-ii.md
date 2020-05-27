---
layout: post
title: 每日一题 - 126.Word Ladder II
tags:
- leetcode
- Hard
- 广度优先搜索
categories: leetcode
description: LeetCode 126.Word Ladder II
---

# 126.Word Ladder II

https://leetcode.com/problems/word-ladder-ii/



## Description

Given two words (*beginWord* and *endWord*), and a dictionary's word list, find all shortest transformation sequence(s) from *beginWord* to *endWord*, such that:

1. Only one letter can be changed at a time
2. Each transformed word must exist in the word list. Note that *beginWord* is *not* a transformed word.

**Note:**

- Return an empty list if there is no such transformation sequence.
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

Output:
[
  ["hit","hot","dot","dog","cog"],
  ["hit","hot","lot","log","cog"]
]
```

**Example 2:**

```
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: []

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```

## Solution 深度优化搜索

```java
class Solution {
    private List<List<String>> answer = new ArrayList<>();

    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        if (wordList == null || wordList.isEmpty()) {
            return this.answer;
        }

        // 快速检测是否有解
        Set<String> set = new HashSet<>(wordList);
        set.remove(beginWord);
        if (!set.contains(endWord)) {
            return this.answer;
        }

        // 通过队列实现广度优先搜索
        Queue<Node> queue = new LinkedList<>();
        Node root = new Node(null, beginWord);
        queue.offer(root);
        this.bfs(queue, endWord, set);

        return this.answer;
    }

    private void bfs(Queue<Node> queue, String endWord, Set<String> wordSet) {
        boolean success = false;
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            Node node = queue.poll();
            // 这里原来是通过 word-word的方式判断的, 但如果用例字符太多, 比现在这种方式更慢
            List<String> nextWords = this.getNextWords(node.word);
            for (String word : nextWords) {
                if (wordSet.contains(word)) {
                    Node newNode = new Node(node, word);
                    // 在该步得到了解, 则该queue访问完了后就可以停止遍历了
                    if (word.equals(endWord)) {
                        this.addToAnswer(newNode);
                        success = true;
                        break;
                    } else {
                        // 可以转换, 则放到下一次访问的队列中
                        queue.offer(newNode);
                    }
                }
            }
            wordSet.remove(node.word);
        }
        if (!success && !queue.isEmpty()) {
            this.bfs(queue, endWord, wordSet);
        }
    }

    private List<String> getNextWords(String word) {
        List<String> words = new ArrayList<>();
        char[] chars = word.toCharArray();
        for (int i = 0; i < word.length(); i++) {
            for (char c = 'a'; c <= 'z'; c++) {
                if (c != word.charAt(i)) {
                    char origin = chars[i];
                    chars[i] = c;
                    words.add(new String(chars));
                    chars[i] = origin;
                }
            }
        }
        return words;
    }

    private void addToAnswer(Node node) {
        List<String> list = new ArrayList<>();
        int index = 0;
        while (node != null) {
            if (index == 0) {
                list.add(node.word);
            } else {
                list.add(0, node.word);
            }
            node = node.prev;
            index++;
        }
        this.answer.add(list);
    }

    static class Node {
        String word;
        // 保存上一个节点, 用于回溯路径
        Node prev;

        Node(Node prev, String word) {
            this.prev = prev;
            this.word = word;
        }
    }
}
```

执行结果

```
Runtime: 1622 ms, faster than 5.02% of Java online submissions for Word Ladder II.
Memory Usage: 49.2 MB, less than 61.54% of Java online submissions for Word Ladder II.
```

