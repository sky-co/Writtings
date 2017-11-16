---
title: LeetCode 695. Max Area of Island
date: 2017-11-16 16:26:13
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - BFS
 - DFS
---

## [题目描述][1]：
Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.
Find the maximum area of an island in the given 2D array. (If there is no island, the maximum area is 0.)
**Example 1:**
```
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
[0,0,0,0,0,0,0,1,1,1,0,0,0],
[0,1,1,0,1,0,0,0,0,0,0,0,0],
[0,1,0,0,1,1,0,0,1,0,1,0,0],
[0,1,0,0,1,1,0,0,1,1,1,0,0],
[0,0,0,0,0,0,0,0,0,0,1,0,0],
[0,0,0,0,0,0,0,1,1,1,0,0,0],
[0,0,0,0,0,0,0,1,1,0,0,0,0]]
```
Given the above grid, return 6. Note the answer is not 11, because the island must be connected 4-directionally.
**Example 2:**
```
[[0,0,0,0,0,0,0,0]]
```
Given the above grid, return 0.
**Note:**
    - The length of each dimension in the given grid does not exceed 50.

## 题目大意：
给定二维格子grid，上下左右相邻的1组成岛屿，求岛屿的最大面积。

### 解题思路1：
---
DFS：深度优先遍历（递归实现）
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/106301/java-c-straightforward-dfs-solution
https://discuss.leetcode.com/topic/106261/java-c-clean-code

**C++代码：**
``` c++
    class Solution
    {
    public:
        int maxAreaOfIsland(vector<vector<int>>& grid)
        {
            int m = grid.size();
            int n = grid[0].size();
            int maxArea = 0;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (1 == grid[i][j]) {
                        maxArea = max(maxArea, dfs(grid, i, j));
                    }
                }
            }
            return maxArea;
        }

    private:
        int dfs(vector<vector<int> >& grid, int i, int j)
        {
            if (0 > i || i >= grid.size() || 0 > j || j >= grid[0].size() || 0 == grid[i][j])
                return 0;

            grid[i][j] = 0; // mark the value to avoid duplication
            return 1 + dfs(grid, i - 1, j) + dfs(grid, i + 1, j)
            + dfs(grid, i, j - 1) + dfs(grid, i, j + 1);
        }
    };
```

**代码分析：**
    1. 递归函数需要注意结束条件，即边界的处理。我们关心的是grid范围内，其值为1的元素
    2. 为避免重复计数，每统计一个grid[i][j]，将其值置为0
    3. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]

### 解题思路2：
---
BFS：广度优先遍历（借助队列迭代实现）
参考LeetCode Discuss：https://discuss.leetcode.com/topic/106274/c-bfs-dfs-concise-code

**C++代码：**
``` c++
    class Solution
    {
    public:
        int maxAreaOfIsland(vector<vector<int>>& grid)
        {
            int m = grid.size();
            int n = grid[0].size();
            int maxArea = 0;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (1 == grid[i][j]) {
                        maxArea = max(maxArea, bfs(grid, i, j));
                    }
                }
            }
            return maxArea;
        }

    private:
        int bfs(vector<vector<int>>& grid, int i, int j)
        {
            queue<pair<int, int>> q;
            q.push(pair<int, int>(i, j));
            int maxArea = 1;
            grid[i][j] = 0; // mark the value to avoid duplication

            vector<int> dir({-1, 0, 1, 0, -1});
            while (0 < q.size()) {
                int m = q.front().first; // row index
                int n = q.front().second; // col index
                q.pop(); // remove from queue

                for (int idx = 0; idx < dir.size() - 1; idx++) {
                    int rowIdx = m + dir[idx];
                    int colIdx = n + dir[idx + 1];
                    if (0<=rowIdx && rowIdx<grid.size() && 0<=colIdx && colIdx<grid[0].size()
                        && 1==grid[rowIdx][colIdx]) {
                        q.push(pair<int, int>(rowIdx, colIdx));
                        maxArea++;
                        grid[rowIdx][colIdx] = 0;
                    }
                }
            }
            return maxArea;
        }
    };
```

**代码分析：**
    1. BFS的处理需要借助数组实现，每一次将grid范围内其值为1的元素塞进数组（后续元素为当前grid[i][j]四个方向上的邻接元素）
    2. 为避免重复计数，每统计一个grid[i][j]，将其值置为0
    3. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]


[1]: https://leetcode.com/problems/max-area-of-island/discuss/
