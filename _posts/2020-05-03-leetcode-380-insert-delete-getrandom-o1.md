---
layout: post
title: 每日一题 - 380.Insert Delete GetRandom O(1) 
tags:
- leetcode
- Design
- Medium
categories: leetcode
description: LeetCode 380.Insert Delete GetRandom O(1) 
---

# 380.Insert Delete GetRandom O(1) 

https://leetcode.com/problems/insert-delete-getrandom-o1/


## Description

Design a data structure that supports all following operations in *average* **O(1)** time.

1. `insert(val)`: Inserts an item val to the set if not already present.
2. `remove(val)`: Removes an item val from the set if present.
3. `getRandom`: Returns a random element from current set of elements. Each element must have the **same probability** of being returned.

**Example:**

```
// Init an empty set.
RandomizedSet randomSet = new RandomizedSet();

// Inserts 1 to the set. Returns true as 1 was inserted successfully.
randomSet.insert(1);

// Returns false as 2 does not exist in the set.
randomSet.remove(2);

// Inserts 2 to the set, returns true. Set now contains [1,2].
randomSet.insert(2);

// getRandom should return either 1 or 2 randomly.
randomSet.getRandom();

// Removes 1 from the set, returns true. Set now contains [2].
randomSet.remove(1);

// 2 was already in the set, so return false.
randomSet.insert(2);

// Since 2 is the only number in the set, getRandom always return 2.
randomSet.getRandom();
```

## Solution 

```java
class RandomizedSet {
    private Set<Integer> set = new HashSet<>();    
    private List<Integer> list = new ArrayList<>();
    private Random random = new Random();

    /** Initialize your data structure here. */
    public RandomizedSet() {
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (!this.set.contains(val)) {
            this.set.add(val);
            this.list.add(val);
            return true;
        }        
        return false;
    }
    
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if (this.set.contains(val)) {
            this.set.remove(val);
            this.list.remove(Integer.valueOf(val));
            return true;
        }
        return false;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        return this.list.get(this.random.nextInt(this.list.size()));
    }
}
```

执行结果

```java
Runtime: 104 ms, faster than 10.60% of Java online submissions for Insert Delete GetRandom O(1).
Memory Usage: 51.5 MB, less than 14.00% of Java online submissions for Insert Delete GetRandom O(1).
```

### 优化remove操作

很明显的是在remove的时候, list的操作为o(n), 肯定是达不到要求的, 所以我们得想一种办法, 在删除的时候的操作为o(1)就好了, 能删除操作在o(1)一般Map才行. 但其实list如果根据index删除也是能达到这个效果的, 所以下面可以根据这个思路去优化.

```java
class RandomizedSet {
    private Map<Integer, Integer> map = new HashMap<>();    
    private List<Integer> list = new ArrayList<>();
    private Random random = new Random();

    /** Initialize your data structure here. */
    public RandomizedSet() {
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (!this.map.containsKey(val)) {
            this.map.put(val, this.list.size());
            this.list.add(val);
            return true;
        }        
        return false;
    }
    
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if (this.map.containsKey(val)) {
            // 获取到被删除元素在list中的index
            int index = this.map.remove(val);
            // 显示如果我们直接根据index删除掉, 还要对后面元素进行移动，导致元素的index发生改变
            // 所以我们必须保证后面元素不动, 而将其删除掉
            // 所以我们现在其实有2种选择
            // 1. 假删除, 这样就肯定还要引入其他东西来做这个, 肯定会更复杂
            // 2. 与最后一个元素交换, 然后只改变最后一个元素的index, 这样其实还是o(1)
            if (index < this.list.size() - 1) {
                Collections.swap(list, index, list.size() - 1);
                this.map.put(this.list.get(index), index);                
            } 
            this.list.remove(list.size() - 1);
            // 可以看到上面3个操作都是o(1), 所以其实还是相当于o(1)的
            return true;
        }
        return false;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        return this.list.get(this.random.nextInt(this.list.size()));
    }
}

/**
 * Your RandomizedSet object will be instantiated and called as such:
 * RandomizedSet obj = new RandomizedSet();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```

执行结果

```java
Runtime: 14 ms, faster than 24.52% of Java online submissions for Insert Delete GetRandom O(1).
Memory Usage: 51.5 MB, less than 14.00% of Java online submissions for Insert Delete GetRandom O(1).
```

