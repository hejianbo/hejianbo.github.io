---
layout: post
title: 每日一题 - 341.Flatten Nested List Iterator
tags:
- leetcode
- 栈
categories: leetcode
description: LeetCode 341.Flatten Nested List Iterator
---

# 341.Flatten Nested List Iterator

https://leetcode.com/problems/flatten-nested-list-iterator/



## Description

Given a nested list of integers, implement an iterator to flatten it.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example 1:**

```
Input: [[1,1],2,[1,1]]
Output: [1,1,2,1,1]
Explanation: By calling next repeatedly until hasNext returns false, 
             the order of elements returned by next should be: [1,1,2,1,1].
```

**Example 2:**

```
Input: [1,[4,[6]]]
Output: [1,4,6]
Explanation: By calling next repeatedly until hasNext returns false, 
             the order of elements returned by next should be: [1,4,6].
```



## Solution 1 

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    private IntegrateIterator current = null;

    public NestedIterator(List<NestedInteger> nestedList) {
        this.current = new IntegrateIterator(nestedList, null);
    }

    @Override
    public Integer next() {
        this.current.index++;
        return this.current.nestedList.get(this.current.index).getInteger();
    }

    @Override
    public boolean hasNext() {
        // 如果越界则往父亲方向上看
        while (this.current != null && this.current.index + 1 >= this.current.nestedList.size()) {
            this.current = this.current.parent;
        }
        if (this.current == null) {
            return false;
        }
        // 如果是整数, 则表示有结果
        NestedInteger nestedInteger = this.current.nestedList.get(this.current.index + 1);
        if (nestedInteger.isInteger()) {
            return true;
        }
        // 如果又是一个List, 则需要继续判断, 不然有可能是一个空List或者嵌套的空List
        this.current.index++;
        this.current = new IntegrateIterator(nestedInteger.getList(), this.current);
        return this.hasNext();
    }
    
        
    static class IntegrateIterator {
        IntegrateIterator parent;
        List<NestedInteger> nestedList;
        int index;

        IntegrateIterator(List<NestedInteger> nestedList, IntegrateIterator parent) {
            this.index = -1;
            this.nestedList = nestedList;
            this.parent = parent;
        }
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

执行结果

```
Runtime: 2 ms, faster than 98.01% of Java online submissions for Flatten Nested List Iterator.
Memory Usage: 42 MB, less than 5.00% of Java online submissions for Flatten Nested List Iterator.

```



## Solution 2 栈

看到Related Topics里面提到了栈， 所以知道这题期望的解法应该是使用栈来解决, 有了提示再去构造结果，就应该挺简单的了。

```java
public class NestedIterator implements Iterator<Integer> {    
    private Stack<NestedInteger> stack = new Stack<>();

    public NestedIterator(List<NestedInteger> nestedList) {
        this.listInStack(nestedList);
    }
    
    private void listInStack(List<NestedInteger> nestedList) {
        for (int i = nestedList.size() - 1; i >= 0; i--) {
            stack.push(nestedList.get(i));
        }
    }

    @Override
    public Integer next() {
        return this.stack.pop().getInteger();
    }

    @Override
    public boolean hasNext() {
        if (this.stack.isEmpty()) {
            return false;
        }
        if (this.stack.peek().isInteger()) {
            return true;
        }
        
        this.listInStack(this.stack.pop().getList());
        return this.hasNext();
    }
}
```

执行结果

```
Runtime: 3 ms, faster than 55.95% of Java online submissions for Flatten Nested List Iterator.
Memory Usage: 41.9 MB, less than 5.00% of Java online submissions for Flatten Nested List Iterator.
```

