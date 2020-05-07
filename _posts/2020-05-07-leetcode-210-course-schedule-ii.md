---
layout: post
title: 每日一题 - 210.Course Schedule II
tags:
- leetcode
- Medium
- 拓朴排序
- 排序
- 深度优化搜索
- 图
categories: leetcode
description: LeetCode 210.Course Schedule II
---

# 210.Course Schedule II

https://leetcode.com/problems/course-schedule-ii/

解此题最好能够先了解一下拓朴排序, 这里我用2种方法进行了解答.

1. 基于图的入度的拓朴排序
2. 基于深度优化搜索的拓朴排序

## Description

There are a total of *n* courses you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs**, return the ordering of courses you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.

**Example 1:**

```
Input: 2, [[1,0]] 
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished   
             course 0. So the correct course order is [0,1] .
```

**Example 2:**

```
Input: 4, [[1,0],[2,0],[3,1],[3,2]]
Output: [0,1,2,3] or [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both     
             courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0. 
             So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3] .
```

**Note:**

1. The input prerequisites is a graph represented by **a list of edges**, not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs).
2. You may assume that there are no duplicate edges in the input prerequisites.



## Solution 1 基于入度的拓朴排序

基于图的入度进行的拓朴排序.  如果对图不熟悉的伙伴，推荐大家看一下imooc上浙大陈老师的视频 。

> 这题虽然很简单, 但在提交的时候老师犯不仔细小错误, 提交了好多次才通过.

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {      
        // 入度
        int[] inDegree = new int[numCourses];
        // [课程1][以课程1为前置的课程]
        int[][] graph = new int[numCourses][numCourses];
        
        for (int i = 0; i < prerequisites.length; i++) {
            // 当前组第一门课程
            int first = prerequisites[i][0];
            // 当前组第二门课程
            int second = prerequisites[i][1];
            // 该组的第一门课程的入度+1, 表示他有一门前置课程
            inDegree[first] += 1;  
            // 表示 first 以 second 为前置课程
            graph[second][first] = 1;
        }
        
        // 将入度为0的课程放进去
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < inDegree.length; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }
        // 结果
        int[] answer = new int[numCourses];    
        int takeCourse = 0;
        while (!queue.isEmpty()) {
            // 入度为0的课程
            int course = queue.poll();                        
            answer[takeCourse] = course;
            takeCourse++;    
            
            // 将以该课程为前置课程的课入度-1
            for (int i = 0; i < graph[course].length; i++) {                
                if (graph[course][i] == 1) {
                    // 将以course为前置课程的入度都减1, 如果入度变为了0, 表示没有前置课程了则入队列
                    if ((--inDegree[i]) == 0) {
                        queue.offer(i);
                    }
                }
            }        
        }
        
        return takeCourse == numCourses ? answer : new int[0];
    }
}
```

执行结果

```
Runtime: 17 ms, faster than 22.21% of Java online submissions for Course Schedule II.
Memory Usage: 71.8 MB, less than 6.10% of Java online submissions for Course Schedule II.
```

## Solution2 基于深度优化搜索的拓朴排序

基于深度优化搜索的拓朴排序是以前在算法导论当中看到的解.

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {      
        // 先构建一张图
        int[][] graph = new int[numCourses][numCourses];     
        for (int i = 0; i < prerequisites.length; i++) {
            // 当前组第一门课程
            int first = prerequisites[i][0];
            // 当前组第二门课程
            int second = prerequisites[i][1];
            graph[first][second] = 1;
        }
        
        // 着色为三种颜色: 0: 默认相当于白色; 1相当于算法导论提到的灰色, 2: 相当于黑色;
        int[][] color = new int[numCourses][numCourses];
        
        List<Integer> answer = new ArrayList<>();
        for (int i = 0; i < graph.length; i++) {
            // DFS
            if (!this.depthFirst(graph, color, i, answer)) {
                return new int[0];
            }
        }
        
        return answer.size() != numCourses ? new int[0] : answer.stream().mapToInt(Integer::valueOf).toArray();
    }
    
    private boolean depthFirst(int[][] graph, int[][] color, int start, List<Integer> answer) {
        // 如果color = 0, 表示此结点还没有访问过
        if (color[start][start] == 0) {
            // 着色为1, 表示正在访问
            color[start][start] = 1;
            // 开始访问其邻接元素, 其其依赖的前置课程
            for (int j = 0; j < graph[start].length; j++) {
                // graph[start][j] 表示存在其元素
                if (start != j && graph[start][j] == 1) {
                    if(!this.depthFirst(graph, color, j, answer)) {
                        return false;
                    }
                }
            }
            // 着色为2, 表示已经访问完了, 则可以入到结果中了
            color[start][start] = 2;
            answer.add(start);
        } else if (color[start][start] == 1){
            // 表示有冲突, 即存在了环
            return false;
        }    
        return true;
    }
}
```

执行结果

```
Runtime: 28 ms, faster than 8.83% of Java online submissions for Course Schedule II.
Memory Usage: 105.6 MB, less than 6.10% of Java online submissions for Course Schedule II.

```

