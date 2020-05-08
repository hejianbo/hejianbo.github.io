---
layout: post
title: 每日一题 - 134.Gas Station
tags:
- leetcode
- Medium
- 贪婪法
categories: leetcode
description: LeetCode 134.Gas Station
---

# 134.Gas Station

https://leetcode.com/problems/gas-station/

## Description

There are *N* gas stations along a circular route, where the amount of gas at station *i* is `gas[i]`.

You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from station *i* to its next station (*i*+1). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return -1.

**Note:**

- If there exists a solution, it is guaranteed to be unique.
- Both input arrays are non-empty and have the same length.
- Each element in the input arrays is a non-negative integer.

**Example 1:**

```
Input: 
gas  = [1,2,3,4,5]
cost = [3,4,5,1,2]

Output: 3

Explanation:
Start at station 3 (index 3) and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
从index3开始, 将油加到 4
Travel to station 4. Your tank = 4 - 1 + 5 = 8
3-4消费cost[3]=1, 然后再加gas[4] = 4-1+5 = 8
Travel to station 0. Your tank = 8 - 2 + 1 = 7
Travel to station 1. Your tank = 7 - 3 + 2 = 6
Travel to station 2. Your tank = 6 - 4 + 3 = 5
Travel to station 3. The cost is 5. Your gas is just enough to travel back to station 3.
Therefore, return 3 as the starting index.
```

**Example 2:**

```
Input: 
gas  = [2,3,4]
cost = [3,4,3]

Output: -1

Explanation:
You can't start at station 0 or 1, as there is not enough gas to travel to the next station.
Let's start at station 2 and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 0. Your tank = 4 - 3 + 2 = 3
Travel to station 1. Your tank = 3 - 3 + 3 = 3
You cannot travel back to station 2, as it requires 4 unit of gas but you only have 3.
Therefore, you can't travel around the circuit once no matter where you start.
```

## Solution 1

根据题意直接写.

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int n = gas.length;
        
        int start = 0;        
        // gas tank中剩余油的数量
        int left = 0;
        // 已经通过的 gas station
        int passed = 0;
        // 当前所在 gas station
        int i = start;            
        while (start < n) {           
            
            int gasCost = i == 0 ? cost[n - 1] : cost[i - 1];
            // 出发位置, 或者剩余油量能够进入下一站
            if (passed == 0 || left - gasCost >= 0) {
                left = passed == 0 ? left + gas[i] : left - gasCost + gas[i];
                // 成功进入下一站
                i = (i + 1 == n ? 0 : i + 1);
                passed++;
            } else {
                // 如果剩余零小于零, 则失败
                start++;
                passed = 0;
                left = 0;
                i = start;                
            }
            
            // 走完一圈, 则表示成功
            if (passed == n + 1) {
                return start;
            }
        }                 
        
        return -1;
    }
    
    
}
```

执行结果

```
Runtime: 46 ms, faster than 16.90% of Java online submissions for Gas Station.
Memory Usage: 39.9 MB, less than 5.88% of Java online submissions for Gas Station.
```

## Solution 2 贪婪

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int n = gas.length;
        
        int start = 0;        
        int leftGas = 0;
        
        // 只有总的gas > 总的cost时, 才有可能绕一圈
        int totalLeftGas = 0;
       
        for (int i = 0; i < n; i++) {
            // 如果剩余的油+当前能加上的油参够走到一下站, 则继续            
            if (leftGas + gas[i] >= cost[i]) {
                leftGas = leftGas + gas[i] - cost[i];
            } else {
                // 如果到达不了, 则重置start到下一个位置
                start = i + 1;
                leftGas = 0;
            }
            
            totalLeftGas += gas[i] - cost[i];
        }
        return totalLeftGas >= 0 ? start : -1;
    }
}
```

执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Gas Station.
Memory Usage: 40.1 MB, less than 5.88% of Java online submissions for Gas Station.
```

