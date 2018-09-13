---
title: LeetCode 766. Toeplitz Matrix
date: 2018-9-5 09:40:59
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
A matrix is Toeplitz if every diagonal from top-left to bottom-right has the same element.
Now given an M x N matrix, return True if and only if the matrix is Toeplitz.

**Example 1:**
```
Input:
matrix = [
  [1,2,3,4],
  [5,1,2,3],
  [9,5,1,2]
]
Output: True
Explanation:
In the above grid, the diagonals are:
"[9]", "[5, 5]", "[1, 1, 1]", "[2, 2, 2]", "[3, 3]", "[4]".
In each diagonal all elements are the same, so the answer is True.
```
**Example 2:**
```
Input:
matrix = [
  [1,2],
  [2,2]
]
Output: False
Explanation:
The diagonal "[1, 2]" has different elements.
```

**Note:**
```
1. matrix will be a 2D array of integers.
2. matrix will have a number of rows and columns in range [1, 20].
3. matrix[i][j] will be integers in range [0, 99].
```

## 题目大意：
Toeplitz（托普利茨）矩阵是指各条从左上到右下对角线元素均相等的矩阵。
给定M x N矩阵，判断是否为Toeplitz矩阵。


### 解题思路1：
---
遍历除了最后一行和最后一列的元素，查看每个元素对角线上的下一个元素是否相等。
参考LeetCode Discuss：
https://leetcode.com/problems/toeplitz-matrix/discuss/113417/Java-solution-4-liner.

**C++代码：**
``` c++
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        for (int i = 0; i < matrix.size() - 1; i++) {
            for (int j = 0; j < matrix[i].size() - 1; j++) {
                if (matrix[i][j] != matrix[i + 1][j + 1]) return false;
            }
        }
        return true;
    }
};
```

### 解题思路2：
---
除了逐个遍历元素以外，观察可得，只需要考虑第一行和第一列的元素（除第一行最后一个元素和第一列最后一个元素以外）是否存在对角线上元素与之相等。


**C++代码：**
``` c++
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        int row = matrix.size();
        int col = matrix[0].size();
        for (int idx = 0; idx < row - 1; idx++) {
            int i = idx;
            int j = 0;
            while(i < row && j < col) {
                if (matrix[idx][0] != matrix[i][j]) {
                    return false;
                }
                i++;
                j++;
            }
        }

        for (int idx = 0; idx < col - 1; idx++) {
            int i = 0;
            int j = idx;
            while(i < row && j < col) {
                if (matrix[0][idx] != matrix[i][j]) {
                    return false;
                }
                i++;
                j++;
            }
        }
        return true;
    }
};
```


[1]: https://leetcode.com/problems/toeplitz-matrix
