---
layout: post
title: 每日一题 - LeetCode 295. Find Median from Data Stream
tags:
- leetcode
categories: leetcode
description: LeetCode 295. Find Median from Data Stream
---

# 295. Find Median from Data Stream


## Description

Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

For example,

`[2,3,4]`, the median is `3`

`[2,3]`, the median is `(2 + 3) / 2 = 2.5`

Design a data structure that supports the following two operations:

- void addNum(int num) - Add a integer number from the data stream to the data structure.
- double findMedian() - Return the median of all elements so far.

 

**Example:**

```
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2
```



## Solution 1 双堆

```java
class MedianFinder {
    private Queue<Integer> min = new PriorityQueue<>();
    private Queue<Integer> max = new PriorityQueue<>(Comparator.reverseOrder());

    /** initialize your data structure here. */
    public MedianFinder() {        
    }
    
    public void addNum(int num) {
        this.max.offer(num);    
        // 平衡
        this.min.offer(this.max.poll());
        // 上面操作会导致max中元素都到min中了, 所以需要将min中的元素移过云
        // 这会导致max.size() >= min.size()
        if (this.max.size() < this.min.size()) {
            this.max.offer(this.min.poll());
        }
    }
    
    public double findMedian() {
        if (this.max.isEmpty()) {
            return 0;
        }
        if (this.max.size() > this.min.size()) {
            return this.max.peek();
        } else {
            return (this.min.peek() + this.max.peek()) * 0.5;
        }
    }
}
```

执行结果

```
Runtime: 54 ms, faster than 34.67% of Java online submissions for Find Median from Data Stream.
Memory Usage: 51.3 MB, less than 100.00% of Java online submissions for Find Median from Data Stream.
```