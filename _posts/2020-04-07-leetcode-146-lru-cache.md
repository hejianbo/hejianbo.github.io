---
layout: post
title: 每日一题 - LeetCode 146.LRU Cache
tags:
- leetcode
- LRU、LFU
categories: leetcode
description: LeetCode 146.LRU Cache
---

# 146.LRU Cache


**最久未使用算法（LRU, Least Recently Used）**：将最长时间未被使用的项从Cache移除。

**最不经常使用算法（LFU, Least Frequently Used）**：将访问次数最少的项从Cache移除。

> 最开始实现的时候居然实现成了LFU, 所以英文还是很重要的..........

## Description

Design and implement a data structure for [Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU). It should support the following operations: `get` and `put`.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
`put(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

The cache is initialized with a **positive** capacity.

**Follow up:**
Could you do both operations in **O(1)** time complexity?

**Example:**

```
LRUCache cache = new LRUCache( 2 /* capacity */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // returns 1
cache.put(3, 3);    // evicts key 2
cache.get(2);       // returns -1 (not found)
cache.put(4, 4);    // evicts key 1
cache.get(1);       // returns -1 (not found)
cache.get(3);       // returns 3
cache.get(4);       // returns 4
```



## Solution: LinkedHashMap

```java
class LRUCache {    
    private int capacity;
    // insertion order
    private Map<Integer, Integer> cache = new LinkedHashMap<>();    
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
    }
    
    public int get(int key) {
        if (!cache.containsKey(key)) {
            return -1;
        }
        
        Integer value = cache.remove(key);
        cache.put(key, value);
        return value;
    }
    
    public void put(int key, int value) {
        if (this.capacity == 0) {
            return;
        }
        // need remove, else don't change insersion order
        if (this.cache.containsKey(key)) {
            this.cache.remove(key);
        }
        this.cache.put(key, value);
        if (this.cache.size() > this.capacity) {
            // remove longest unused key
            int longestKey = this.cache.keySet().iterator().next();
            this.cache.remove(longestKey);
        }
    }
        
}
```
