---
layout: post
title: 每日一题 - 395.Longest Substring with At Least K Repeating Characters
tags:
- leetcode
- Medium
categories: leetcode
description: LeetCode 395.Longest Substring with At Least K Repeating Characters
---
# 395.Longest Substring with At Least K Repeating Characters

https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/



## Description

Find the length of the longest substring **T** of a given string (consists of lowercase letters only) such that every character in **T** appears no less than *k* times.

**Example 1:**

```
Input:
s = "aaabb", k = 3

Output:
3

The longest substring is "aaa", as 'a' is repeated 3 times.
```

**Example 2:**

```
Input:
s = "ababbc", k = 2

Output:
5

The longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.
```

## Solution 

因为要满足子串中的字符出现次数最少的都大于k, 所以可以知道:

- 如果所有字符出现的次数都比k大, 则整个字符串都能满足条件
- 如果某个字符出现的总次数比k小, 则一定不能满足条件

所以我们一定可以根据不能满足条件的字符作为分隔符, 将字符串分开, 然后再重复上面的步骤, 一定能得到解.

```java
class Solution {
    private int longest = 0;
    
    public int longestSubstring(String s, int k) {     
        
        this.helper(s, k);
        
        return this.longest;
    }
    
    private void helper(String s, int k) {
        // 裁枝
        if (s.length() < k || s.length() < this.longest) {
            return;
        }
        // 统计次数
        Map<Character, Integer> map = new HashMap<>();        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            map.put(c, map.getOrDefault(c, 0) + 1);    
        }
        
        // 通过队列获取次数最小的字符
        Queue<Node> queue = new PriorityQueue<>((o1, o2) -> o1.times.compareTo(o2.times));              
        for (char c : map.keySet()) {
            queue.offer(new Node(c, map.get(c)));
        }
        // 如果最小的字符满足条件, 则该串满足条件
        if (queue.peek().times >= k) {
            this.longest = Math.max(this.longest, s.length());
            return;
        }
        // 以不满足条件的字符进行分隔
        String[] children = s.split(String.valueOf(queue.peek().c));
        for (String child : children) {              
            this.helper(child, k);                                        
        }        
                
    }
    
   static class Node {
        char c;
        Integer times = 0;
        Node(char c, int times) {
            this.c = c;
            this.times = times;
        }
    }    
}
```

执行结果

```
Runtime: 5 ms, faster than 39.43% of Java online submissions for Longest Substring with At Least K Repeating Characters.
Memory Usage: 39.4 MB, less than 10.00% of Java online submissions for Longest Substring with At Least K Repeating Characters.
```

先稍微优化一下里面使用的数据结构, 毕竟在这里没有必要这么浪费.

```java
class Solution {
    private int longest = 0;
    
    public int longestSubstring(String s, int k) {     
        
        this.helper(s, k);
        
        return this.longest;
    }
    
    private void helper(String s, int k) {
        if (s.length() < k || s.length() < this.longest) {
            return;
        }
        int[] counts = new int[26];
        for (int i = 0; i < s.length(); i++) {
            counts[s.charAt(i) - 'a'] += 1;
        }
        
        int index = -1;
        int minCount = Integer.MAX_VALUE;
        for (int i = 0; i < counts.length; i++) {
            if (counts[i] > 0 && counts[i] < minCount) {
                minCount = counts[i];
                index = i;
            }
        }
        
        // 如果最小的字符满足条件, 则该串满足条件
        if (minCount >= k) {
            this.longest = Math.max(this.longest, s.length());
            return;
        }
        // 以不满足条件的字符进行分隔
        String[] children = s.split(String.valueOf((char)(index + 'a')));        
        for (String child : children) {              
            this.helper(child, k);                                        
        }       
                
    }
}
```

```java
Runtime: 2 ms, faster than 65.12% of Java online submissions for Longest Substring with At Least K Repeating Characters.
Memory Usage: 37.8 MB, less than 20.00% of Java online submissions for Longest Substring with At Least K Repeating Characters.
```

还可以进行一下些优化, 但从根本上来讲, 基于这个解法优化的空间已经不大了, 除非换其它解法， 下面在这种解决上做进一步的优化看一下(主要思路就是在看什么地方可以剪枝, 缩小处理的数据量):

- 我们在判断是否满足条件的时候, 当前看的是min最小出现的字符, 基于相对的, 出现最多的字符可以用来进行前枝, 因为多一个判断在一个循环内相当于O(1), 如果剪枝成功, 当量大的时候还是能缩短时间的
- 在字符串拆分后, 理论上可以进行排序, 从长的开始处理, 如果长的满足了, 根据方法如果的剪枝条件, 就会跳过处理短的字符串

优化后代码如下, 确实快了一点, 但里面的排序效果从执行的情况来看, 在这里用处不大.

```java
class Solution {
    private int longest = 0;
    
    public int longestSubstring(String s, int k) {     
        
        this.helper(s, k);
        
        return this.longest;
    }
    
    private void helper(String s, int k) {
        // 剪枝
        if (s.length() < k || s.length() <= this.longest) {
            return;
        }
        int[] counts = new int[26];
        for (int i = 0; i < s.length(); i++) {
            counts[s.charAt(i) - 'a'] += 1;
        }
        
        int minIndex = -1;
        int minCount = Integer.MAX_VALUE;
        int maxCount = 0;
        for (int i = 0; i < counts.length; i++) {
            if (counts[i] > 0 && counts[i] < minCount) {
                minCount = counts[i];
                minIndex = i;
            }
            maxCount = Math.max(maxCount, counts[i]);
        }
        // 剪枝
        if (maxCount < k) {
            return;
        }
                                                 
        // 如果最小的字符满足条件, 则该串满足条件
        if (minCount >= k) {
            this.longest = Math.max(this.longest, s.length());
            return;
        }
        // 以不满足条件的字符进行分隔
        String[] children = s.split(String.valueOf((char)(minIndex + 'a')));
        // 排序后, 有可能避免后面不必要处理
        Arrays.sort(children, (o1, o2) -> Integer.compare(o2.length(), o1.length()));
        for (String child : children) {              
            this.helper(child, k);                                        
        }        
                
    }
}
```

执行结果

```
Runtime: 2 ms, faster than 65.12% of Java online submissions for Longest Substring with At Least K Repeating Characters.
Memory Usage: 38 MB, less than 10.00% of Java online submissions for Longest Substring with At Least K Repeating Characters.

```

再次优化一下,  其实根据上面的执行结果, 优化的空间已经不大了.  主要是对一些细节进行调整.

```java
class Solution {
    private int longest = 0;
    
    public int longestSubstring(String s, int k) {    
        this.helper(s, k);        
        return this.longest;
    }
    
    private void helper(String s, int k) {
        if (s.length() < k || s.length() <= this.longest) {
            return;
        }
        int[] counts = new int[26];
        for (int i = 0; i < s.length(); i++) {     
            int index = s.charAt(i) - 'a';
            counts[index] += 1;
        }
        
        int minCount = k;
        for (int i = 0; i < counts.length; i++) {
            if (counts[i] > 0) {
                minCount = Math.min(minCount, counts[i]);
            }
        }
        if (minCount >= k) {
            this.longest = Math.max(this.longest, s.length());    
        }
        
        StringBuilder child = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (counts[s.charAt(i) - 'a'] < k) {
                this.helper(child.toString(), k);
                child.delete(0, child.length());
            } else {
                child.append(s.charAt(i));
            }
        }      
        // 处理掉剩余的字符
        this.helper(child.toString(), k);
    }
}
```

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Longest Substring with At Least K Repeating Characters.
Memory Usage: 38 MB, less than 20.00% of Java online submissions for Longest Substring with At Least K Repeating Characters.

```

