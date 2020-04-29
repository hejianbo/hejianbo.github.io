---
layout: post
title: 每日一题 - 289.Game of Life
tags:
- leetcode
categories: leetcode
description: LeetCode 289.Game of Life
---

# 289.Game of Life

According to the [Wikipedia's article](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life): "The **Game of Life**, also known simply as **Life**, is a cellular automaton devised by the British mathematician John Horton Conway in 1970."

Given a *board* with *m* by *n* cells, each cell has an initial state *live* (1) or *dead* (0). Each cell interacts with its [eight neighbors](https://en.wikipedia.org/wiki/Moore_neighborhood)(horizontal, vertical, diagonal) using the following four rules (taken from the above Wikipedia article):

1. Any live cell (with fewer than two live neighbors) dies, as if caused by under-population. 
2. Any live cell (with two or three live neighbors) lives on to the next generation.
3. Any live cell (with more than three live neighbors) dies, as if by over-population..
4. Any dead cell (with exactly three live neighbors becomes a live cell), as if by reproduction.

Write a function to compute the next state (after one update) of the board given its current state. The next state is created by applying the above rules simultaneously to every cell in the current state, where births and deaths occur simultaneously.

**Example:**

```
Input: 
[
  [0,1,0],
  [0,0,1],
  [1,1,1],
  [0,0,0]
]
Output: 
[
  [0,0,0],
  [1,0,1],
  [0,1,1],
  [0,1,0]
]
```

**Follow up**:

1. Could you solve it in-place? Remember that the board needs to be updated at the same time: You cannot update some cells first and then use their updated values to update other cells.
2. In this question, we represent the board using a 2D array. In principle, the board is infinite, which would cause problems when the active area encroaches the border of the array. How would you address these problems?



## Solution 1

```java
/*
* 1. 规则1: 任何活细胞周围少于2个活细胞，则它会变为死细胞
* 2. 规则2: 任何活细胞周围有2个或者3个个活细胞，则它不会死
* 3. 规则3: 任何活细胞周围超过3个活细胞，则它会死
* 4. 规则4: 任何死细胞周围刚好有3个活细胞，则它会变成活细胞
*/
class Solution {
    private int[] neighbors = {-1, 0, 1};
    
    public void gameOfLife(int[][] board) {
        if (board == null || board.length == 0) {
            return;
        }
        
        int n = board.length;
        int m = board[0].length;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                boolean liveCell = board[i][j] == 1;
                // 周围活细胞数量
                int liveNeightbors = this.countLiveNeighbors(board, i, j);
                // 如果活细胞周围只有2个或者3个活细胞, 则不会死,否则变为死细胞            
                if (liveCell && liveNeightbors != 2 && liveNeightbors != 3) {
                    // 2 表示原来是活的, 所以稍后还要将它还原为0
                    board[i][j] = 2;                    
                }
                // 如果死细胞周围只有3个活细胞, 则它会变成活细胞
                if (!liveCell && liveNeightbors == 3){
                    // -1 表示原来是死的, 所以稍后需要还原为1
                    board[i][j] = -1;
                }
            }
        }
        
        // 将临时状态-1, 2更新为1, 0
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (board[i][j] == -1) {
                    board[i][j] = 1;
                }
                if (board[i][j] == 2) {
                    board[i][j] = 0;
                }
            }
        }
    }
    
    private int countLiveNeighbors(int[][] board, int row, int column) {
        // 8个位置
        int count = 0;
        for (int i = 0; i < this.neighbors.length; i++) {            
            int newRow = row + this.neighbors[i];
            if (newRow >= 0 && newRow < board.length) {
                for (int j = 0; j < this.neighbors.length; j++) {                
                    int newColumn = column + this.neighbors[j];
                    if (newColumn >= 0 && newColumn < board[0].length
                        // 9个位置中排除当前元素所在位置
                        && !(this.neighbors[i] == 0 && this.neighbors[j] == 0)) {
                        if (board[newRow][newColumn] == 1 || board[newRow][newColumn] == 2) {
                            count++;
                        }   
                    }
                }
            }
        }
        return count;
    }
}
```



执行结果

```
Runtime: 0 ms, faster than 100.00% of Java online submissions for Game of Life.
Memory Usage: 37.7 MB, less than 7.69% of Java online submissions for Game of Life.
```

