---
title: LeetCode 200. Number of Islands
date: 2017-11-17 09:40:59
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
Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.
**Example 1:**
```
11110
11010
11000
00000
```
Answer: 1
**Example 2:**
```
11000
11000
00100
00011
```
Answer: 3
## 题目大意：
给定一个由字符‘1’（陆地）和‘0’（水域）组成的二维网格地图，计算岛屿的个数。岛屿被水域环绕，由竖直或者水平方向邻接的陆地构成。你可以假设网格地图的四条边都被水域包围。
测试样例见题目描述

### 解题思路1：
---
DFS：深度优先遍历（递归实现）
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/106261/java-c-clean-code
https://discuss.leetcode.com/topic/13045/my-accepted-c-solution-may-be-trivial

**C++代码：**
``` c++
class Solution
{
public:
    int numIslands(vector<vector<char> >& grid)
    {
        if (0 == grid.size() || 0 == grid[0].size())
            return 0;

        int numIslands = 0;
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[0].size(); j++) {
                if ('1' == grid[i][j]) {
                    numIslands++;
                    bfs(grid, i, j);
                }
            }
        }
        return numIslands;
    }

private:
    void dfs(vector<vector<char> >& grid, int i, int j)
    {
        if (0 > i || i >= grid.size() || 0 > j || j >= grid[0].size() || '0' == grid[i][j])
            return;

        grid[i][j] = '0'; // mark the value to avoid duplication
        vector<int> dir({-1, 0, 1, 0, -1}); // c++11/14
        // int nums[] = {-1, 0, 1, 0, -1};
        // vector<int> dir(nums, nums + sizeof(nums) / sizeof(int));
        for (int idx = 0; idx < dir.size() - 1; idx++) {
            int rowIdx = i + dir[idx]; // row index
            int colIdx = j + dir[idx + 1]; // col index
            dfs(grid, rowIdx, colIdx);
        }
    }
};
```

**代码分析：**
    1. 递归函数需要注意结束条件，即边界的处理。我们关心的是grid范围内，其值为1的元素
    2. 为避免重复计数，每统计一个grid[i][j]，将其值置为0
    3. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]
    4. 由于是字符数组，在dfs迭代过程中，先要判断是否存在空数组情况，即‘[]’.

### 解题思路2：
---
BFS：广度优先遍历（借助队列迭代实现）
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/11589/dfs-and-bfs-in-c

**C++代码：**
``` c++
class Solution
{
public:
    int numIslands(vector<vector<char> >& grid)
    {
        if (0 == grid.size() || 0 == grid[0].size())
            return 0;

        int numIslands = 0;
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[0].size(); j++) {
                if ('1' == grid[i][j]) {
                    numIslands++;
                    bfs(grid, i, j);
                }
            }
        }
        return numIslands;
    }

private:
    void bfs(vector<vector<char> >& grid, int i, int j)
    {
        queue<pair<int, int> > q;
        q.push(pair<int, int>(i, j));
        grid[i][j] = '0'; // mark the value to avoid duplication
        vector<int> dir({-1, 0, 1, 0, -1}); // c++11/14
        // int nums[] = {-1, 0, 1, 0, -1};
        // vector<int> dir(nums, nums + sizeof(nums) / sizeof(int));
        while (0 < q.size()) {
            int m = q.front().first; // row index
            int n = q.front().second; // col index
            q.pop(); // remove from queue
            for (int idx = 0; idx < dir.size() - 1; idx++) {
                int rowIdx = m + dir[idx];
                int colIdx = n + dir[idx + 1];
                if (0<=rowIdx && rowIdx<grid.size() && 0<=colIdx && colIdx<grid[0].size()
                    && '1'==grid[rowIdx][colIdx]) {
                    q.push(pair<int, int>(rowIdx, colIdx));
                    grid[rowIdx][colIdx] = '0';
                }
            }
        }
    }
};
```

**代码分析：**
    1. BFS的处理需要借助数组实现，每一次将grid范围内其值为1的元素塞进数组（后续元素为当前grid[i][j]四个方向上的邻接元素）
    2. 为避免重复计数，每统计一个grid[i][j]，将其值置为0
    3. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]
    4. 由于是字符数组，在dfs迭代过程中，先要判断是否存在空数组情况，即‘[]’.


[1]: https://leetcode.com/problems/number-of-islands/description/
