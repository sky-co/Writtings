---
title: LeetCode 463. Island Perimeter
date: 2017-11-17 16:09:05
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - BFS
 - DFS
---


## [题目描述][1]：
You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded by water, and there is exactly one island (i.e., one or more connected land cells). The island doesn't have "lakes" (water inside that isn't connected to the water around the island). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.
**Example:**
```
[[0,1,0,0],
[1,1,1,0],
[0,1,0,0],
[1,1,0,0]]

Answer: 16
```
Explanation: The perimeter is the 16 yellow stripes in the image below:
{% qnimg 2017-11-17/2017_11_17_1.png %}

## 题目大意：
给定一个二维地图，1表示陆地，0表示水域。单元格水平或者竖直相连（不含对角线）。地图完全被水域环绕，只包含一个岛屿（也就是说，一个或者多个相连的陆地单元格）。岛屿没有湖泊（岛屿内部环绕的水域）。单元格是边长为1的正方形。地图是矩形，长宽不超过100。计算岛屿的周长。

### 解题思路1：
---
DFS：深度优先遍历（递归实现）, 虑每个元素的四个方向，如果是边界或者0，则perimeter+1。
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/106301/java-c-straightforward-dfs-solution
https://discuss.leetcode.com/topic/106261/java-c-clean-code

**C++代码：**
``` c++
    class Solution {
    public:
        int islandPerimeter(vector<vector<int> >& grid)
        {
            int m = grid.size();
            int n = grid[0].size();
            int nums = 0;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (1 == grid[i][j])
                        nums += bfs(grid, i, j);
                }
            }
            return nums;
        }

    private:
        int dfs(vector<vector<int> >& grid, int i, int j)
        {
            if (i < 0 || i >= grid.size() || j < 0 || j >= grid[0].size()
                || 0 == grid[i][j])
                return 1; // stripes
            else if (-1 == grid[i][j])
                return 0;
            else {
                grid[i][j] = -1; // mark visited
                return dfs(grid, i - 1, j) + dfs(grid, i + 1, j)
                + dfs(grid, i, j - 1) + dfs(grid, i, j + 1);
            }
        }
    };
```

**代码分析：**
    1. 递归函数需要注意结束条件，即边界的处理。我们关心的是grid范围内，其值为1的元素
    2. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]
    3. 如果是边界点或者grid[i][j]为0的情况，则perimeter + 1
    4. 访问过的元素置为-1，与原数组元素值区分。
    5. 二维数组的vector表示
    ```C++
    int arr[4][4] = {{0,1,0,0},{1,1,1,0},{0,1,0,0},{1,1,0,0}};
    vector<vector<int> > grid(4);
    for (int i = 0; i < grid.size(); i++)
        grid[i].resize(4);
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            grid[i][j] = arr[i][j];
        }
    }
    ```

### 解题思路2：
---
BFS：广度优先遍历（借助队列迭代实现）
参考LeetCode Discuss：

**C++代码：**
``` c++
class Solution {
public:
    int islandPerimeter(vector<vector<int> >& grid)
    {
        int m = grid.size();
        int n = grid[0].size();
        int nums = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (1 == grid[i][j])
                    nums += bfs(grid, i, j);
            }
        }
        return nums;
    }

private:
    int bfs(vector<vector<int> >& grid, int i, int j)
    {
        queue<pair<int, int> > q;
        grid[i][j] = -1; // visited

        vector<int> dir({-1, 0, 1, 0, -1}); // c++11/14
        // int nums[] = {-1, 0, 1, 0, -1};
        // vector<int> dir(nums, nums + sizeof(nums) / sizeof(int));
        q.push(pair<int, int>(i, j));
        int nums = 0;
        while (0 < q.size()) {
            int m = q.front().first; // row index
            int n = q.front().second; // col index
            q.pop(); // remove from queue

            for (int idx = 0; idx < dir.size() - 1; idx++) {
                int rowIdx = m + dir[idx]; // row index
                int colIdx = n + dir[idx + 1]; // col index
                if (rowIdx < 0 || rowIdx >= grid.size() || colIdx < 0 || colIdx >= grid[0].size()
                    || 0 == grid[rowIdx][colIdx])
                    nums++; //  accumulate stripes
                else if (-1 == grid[rowIdx][colIdx])
                    continue; // skip visted item
                else {
                    q.push(pair<int, int>(rowIdx, colIdx));
                    grid[rowIdx][colIdx] = -1; // mark visited
                }
            }
        }
        return nums;
    }
};
```

**代码分析：**
    1. 递归函数需要注意结束条件，即边界的处理。我们关心的是grid范围内，其值为1的元素
    2. 由于孤岛有四个维度，元素grid[i][j]的四个方向的下一个元素坐标分别为：[(i-1, j), (i+1, j), (i, j-1), (i, j+1)]
    3. 如果是边界点或者grid[i][j]为0的情况，则perimeter + 1
    4. 访问过的元素置为-1，与原数组元素值区分。
    5. 二维数组的vector表示
    ```C++
    int arr[4][4] = {{0,1,0,0},{1,1,1,0},{0,1,0,0},{1,1,0,0}};
    vector<vector<int> > grid(4);
    for (int i = 0; i < grid.size(); i++)
        grid[i].resize(4);
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            grid[i][j] = arr[i][j];
        }
    }
    ```


[1]: https://leetcode.com/problems/island-perimeter/description/
