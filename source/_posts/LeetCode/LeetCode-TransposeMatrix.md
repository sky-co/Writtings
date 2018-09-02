---
title: LeetCode 867. Transpose Matrix
date: 2018-8-30 09:40:59
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
Given a matrix A, return the transpose of A.
The transpose of a matrix is the matrix flipped over it's main diagonal, switching the row and column indices of the matrix.

**Example 1:**
```
Input: [[1,2,3],[4,5,6],[7,8,9]]
Output: [[1,4,7],[2,5,8],[3,6,9]]
```
**Example 2:**
```
Input: [[1,2,3],[4,5,6]]
Output: [[1,4],[2,5],[3,6]]
```

**Note:**
```
1 <= A.length <= 1000
1 <= A[0].length <= 1000
```

## 题目大意：
把一个矩阵按照主对角线，翻转行列的数值。
测试样例见题目描述

### 解题思路：
其实就是讲远矩阵的行列颠倒过来，按主对角线翻转就是元素的行列索引值对调。

---
参考LeetCode Discuss：
https://leetcode.com/problems/transpose-matrix/discuss/146797/C++JavaPython-Easy-Understood


**C++代码：**
``` c++
class Solution {
public:
    vector<vector<int>> transpose(vector<vector<int>>& A) {
        vector<vector<int> > vRes;
        if (0 == A.size()) return vRes;

        int row = A.size();
        int col = A[0].size();
        for (int colIdx = 0; colIdx < col; colIdx++) {
            vector<int> v;
            for (int rowIdx = 0; rowIdx < row; rowIdx++) {
                v.push_back(A[rowIdx][colIdx]);
            }
            vRes.push_back(v);
        }
        return vRes;
    }
};
```


[1]: https://leetcode.com/problems/transpose-matrix
