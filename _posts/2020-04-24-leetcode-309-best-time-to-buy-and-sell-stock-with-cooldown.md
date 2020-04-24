---
layout: post
title: 每日一题 - 309. Best Time to Buy and Sell Stock with Cooldown
tags:
- leetcode
- 动态规划
categories: leetcode
description: LeetCode 309. Best Time to Buy and Sell Stock with Cooldown
---

# 309. Best Time to Buy and Sell Stock with Cooldown

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

## Description

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:

- You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
- After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)

**Example:**

```
Input: [1,2,3,0,2]
Output: 3 
Explanation: transactions = [buy, sell, cooldown, buy, sell]
```



## Solution 动态规划

多状态转移问题.

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        
        // 持有状态, 由休息状态-买入，或者持有状态-继续持久得到
        int[] hold = new int[n + 1];
        hold[0] = Integer.MIN_VALUE;
        // 集出状态, 由持有状态-卖出得到
        int[] sold = new int[n + 1];
        // 休息状态, 由售出状态-休息得到，或者继续休息
        int[] rest = new int[n + 1];        
        
        int maxProfit = 0;
        for (int i = 0; i < n; i++) {            
            rest[i+1] = Math.max(rest[i], sold[i]);
            hold[i+1] = Math.max(hold[i], rest[i] - prices[i]);
            sold[i+1] = hold[i] + prices[i];
        }       
        
        return Math.max(sold[n], rest[n]);
    }
}
```

执行结果

```
Runtime: 1 ms, faster than 58.88% of Java online submissions for Best Time to Buy and Sell Stock with Cooldown.
Memory Usage: 37.7 MB, less than 29.63% of Java online submissions for Best Time to Buy and Sell Stock with Cooldown.

```

优化

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        
        int restMaxProfit = 0;
        int soldMaxProfit = 0;
        int holdMaxProfit = Integer.MIN_VALUE;
        
        for (int i = 0; i < n; i++) {
            int preHoldMaxProfit = holdMaxProfit;
            holdMaxProfit = Math.max(holdMaxProfit, restMaxProfit - prices[i]);
            restMaxProfit = Math.max(restMaxProfit, soldMaxProfit);            
            soldMaxProfit = preHoldMaxProfit + prices[i];
        }       
        
        return Math.max(soldMaxProfit, restMaxProfit);
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Best Time to Buy and Sell Stock with Cooldown.
Memory Usage: 37.3 MB, less than 37.04% of Java online submissions for Best Time to Buy and Sell Stock with Cooldown.
```

