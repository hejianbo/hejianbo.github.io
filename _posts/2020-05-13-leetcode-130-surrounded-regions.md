---
layout: post
title: 每日一题 - 130.Surrounded Regions
tags:
- leetcode
- Medium
- 并查集
- 广度优化搜索
categories: leetcode
description: LeetCode 130.Surrounded Regions
---

# 130.Surrounded Regions

https://leetcode.com/problems/surrounded-regions/

## Description

Given a 2D board containing `'X'` and `'O'` (**the letter O**), capture all regions surrounded by `'X'`.

A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region.

**Example:**

```
X X X X
X O O X
X X O X
X O X X
```

After running your function, the board should be:

```
X X X X
X X X X
X X X X
X O X X
```

**Explanation:**

Surrounded regions shouldn’t be on the border, which means that any `'O'` on the border of the board are not flipped to `'X'`. Any `'O'` that is not on the border and it is not connected to an `'O'` on the border will be flipped to `'X'`. Two cells are connected if they are adjacent cells connected horizontally or vertically.



## Solution 1. 广度优化搜索

先来个直接点的解, 因为所有与边界相连的'O'都不会变为'X', 所以这个解就直接从边界出发, 按广度优化搜索的顺序将所有与边界相连的格式都进行标记, 最后将所有没有标记的格子全部变为'X'.

```java
class Solution {
    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return;
        }
        int rows = board.length;
        int columns = board[0].length;
        
        // 保存边界坐标0:row, 1:colum       
        Queue<int[]> queue = new LinkedList<>();
        // 1表示不需要重置
        int[][] visit = new int[rows][columns];
        
        // 获取所有边界为'O'的的坐标, 并放入队列
        for (int i = 0; i < rows; i++) {
            if (board[i][0] == 'O') {
                queue.offer(new int[] {i, 0});
            }
            if (board[i][columns - 1] == 'O') {
                queue.offer(new int[] {i, columns - 1});
            }
            visit[i][0] = 1;
            visit[i][columns - 1] = 1;
        }
        // 获取所有边界为'O'的的坐标, 并放入队列
        for (int i = 0; i < columns; i++) {
            if (board[0][i] == 'O') {
                queue.offer(new int[] {0, i});
            }
            if (board[rows-1][i] == 'O') {
                queue.offer(new int[] {rows - 1, i});
            }
            visit[0][i] = 1;
            visit[rows - 1][i] = 1;
        }
        
        // 从边界出发, 标记所有与其相连的值为'O'的坐标
        while (!queue.isEmpty()) {
            int[] node = queue.poll();
            int row = node[0];
            int column = node[1];
            // 上
            if (row - 1 >= 0 && visit[row - 1][column] == 0 && board[row - 1][column] == 'O') {
                visit[row-1][column] = 1;
                queue.offer(new int[] {row-1, column});
            }
            // 下
            if (row + 1 < rows && visit[row + 1][column] == 0 && board[row + 1][column] == 'O') {
                visit[row+1][column] = 1;
                queue.offer(new int[] {row+1, column});
            }
            // 左
            if (column - 1 >= 0 && visit[row][column - 1] == 0 && board[row][column - 1] == 'O') {
                visit[row][column-1]= 1;
                queue.offer(new int[] {row, column - 1});
            }
            // 右
            if (column +1 < columns && visit[row][column + 1] == 0 && board[row][column + 1] == 'O') {
                visit[row][column+1]= 1;
                queue.offer(new int[] {row, column + 1});                           
            }
        }        
        
        // 将所有没有标记的'O'变为'X'
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (board[i][j] == 'O' && visit[i][j] == 0) {
                    board[i][j] = 'X';
                }
            }
        }
    }
}
```

执行时间 

```
Runtime: 2 ms, faster than 55.51% of Java online submissions for Surrounded Regions.
Memory Usage: 41.7 MB, less than 64.29% of Java online submissions for Surrounded Regions.
```

## Solution 2. Union Find

通过UnionFind数据结构来实现其实也比较容易理解:

- 将每个值'O'的格子看成一个图中的一个顶点 
- 如果某个顶点与其它顶点相邻, 则通过UnionFind的Union操作将它们连接起来
- 如果在合并后中所有顶点中有一个顶点与边界相领, 则在这个集合中的所有顶点都相当于与边界相邻了.

```java
class Solution {
    
    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return;
        }
        int rows = board.length;
        int columns = board[0].length;
        
        // 通过并查集处理将所有为'O'的连接起来
        UnionFind unionFind = new UnionFind(rows,columns);
        for (int row = 0; row < rows; row++) {
            for (int column = 0; column < columns; column++) {
                if (board[row][column] == 'O') {
                    boolean isBorder = this.isBorder(row, column, rows, columns);
                    Node node = unionFind.add(row, column, isBorder);
                    // 上
                    if (row - 1 >= 0 && board[row-1][column] == 'O') {
                        Node topNode = unionFind.find(row-1, column);
                        unionFind.union(node.isBorder ? topNode : node, node.isBorder ? node : topNode);
                    }
                    if (column - 1 >= 0 && board[row][column - 1] == 'O') {
                        Node leftNode = unionFind.find(row, column - 1);
                        unionFind.union(node.isBorder ? leftNode : node, node.isBorder ? node : leftNode);
                    }
                }
            }
        }
        
        for (int row = 0; row < rows; row++) {
            for (int column = 0; column < columns; column++) {
                if (board[row][column] == 'O') {
                    Node node = unionFind.find(row, column);
                    if (node != null && !node.isBorder) {
                        board[row][column] = 'X';
                    }
                }
            }
        }
    }
    
    private boolean isBorder(int row, int column, int rows, int columns) {
        if (row == 0 || column == 0) {
            return true;
        }
        if (row == rows - 1 || column == columns - 1) {
            return true;
        }
        
        return false;
    }
    
    static class UnionFind {
        private Map<Integer, List<Node>> map = new HashMap<>();   
        private int group = 1;
        private int[][] cache;        
        
        UnionFind(int rows, int columns) {
            this.cache = new int[rows][columns];
        }
        
        // 添加一个新顶点进来, 单独放到一组
        public Node add(int row, int column, boolean isBoard) {
            this.group++;            
            List<Node> list = new ArrayList<>();
            Node node = new Node(row, column, isBoard, this.group);
            list.add(node);            
            this.map.put(this.group, list);
            cache[row][column] = group;
            return node;
        }
        
        // 根据坐标找到标点
        public Node find(int row, int column) {
            int currentGroup = this.cache[row][column];
            if (currentGroup == 0) {
                return null;
            }
            // 因为在这里只需要找到是属于那一组, 所以直接返回其中一个元素就行了
            // 这样就可以将find提升到O(1)了
            if (this.map.containsKey(currentGroup)) {
                return this.map.get(currentGroup).get(0);
            }
            return null;
        }
        
        // 合并两个顶点
        public void union(Node firstNode, Node secondNode) {
            if (firstNode.group == secondNode.group) {
                return;
            }
            
            boolean isBorder = firstNode.isBorder || secondNode.isBorder;
            int firstGroup = firstNode.group;
            
            
            List<Node> list = this.map.get(firstNode.group); 
            // 把原来的一组元素清掉
            this.map.remove(firstGroup);
            
            // 将firstNode合并到second中
            for (Node node : list) {
                node.group = secondNode.group;
                node.isBorder = isBorder;
                this.cache[node.row][node.column] = secondNode.group;
                this.map.get(secondNode.group).add(node);
            }            
        }
    }
    
    static class Node {
        int row;
        int column;
        int group;        
        boolean isBorder;
        Node(int row, int column, boolean isBorder, int group) {
            this.row = row;
            this.column = column;
            this.isBorder = isBorder;
            this.group = group;
        }        
    }
}
```

执行结果

```
Runtime: 26 ms, faster than 5.02% of Java online submissions for Surrounded Regions.
Memory Usage: 41.9 MB, less than 60.71% of Java online submissions for Surrounded Regions.
```

从这里可以看到这个结果其实不理想的, 因为在union的时候没有对路径进行压缩, 导致会很慢, 有时间再来优化吧.