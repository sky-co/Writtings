---
title: LeetCode 905. Sort Array By Parity
date: 2018-9-24 09:40:59
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
Given an array A of non-negative integers, return an array consisting of all the even elements of A, followed by all the odd elements of A.

You may return any answer array that satisfies this condition.

**Example 1:**
```
Input: [3,1,2,4]
Output: [2,4,3,1]
The outputs [4,2,3,1], [2,4,1,3], and [4,2,1,3] would also be accepted.
```

**Note:**
```
1 <= A.length <= 5000
0 <= A[i] <= 5000
```

## 题目大意：
给定一个包含非负数整数的数组，返回一个数组，数组内包含所有的偶数，偶数后跟着A所有的奇数元素。

### 解题思路1：
其实就是判断奇偶数，然后索引值对调。

---

**C++代码：**
``` c++
class Solution {
public:
    vector<int> sortArrayByParity(vector<int>& A) {
        int size = A.size();
        vector<int> v(size, 0);
        int startIdx = 0;
        int endIdx = size - 1;
        for (int idx = 0; idx < size; idx++) {
            if (A[idx] & 1) v[endIdx--] = A[idx];
            else v[startIdx++] = A[idx];
        }
        return v;
    }
};
```

### 解题思路2：
相比第一种解法，思路2的方法差不多，时间复杂度O(n)，不同之处在于，思路2没有新增加空间，空间复杂度不变，原地操作元素。

---
参考LeetCode Discuss：
https://leetcode.com/problems/sort-array-by-parity/discuss/170734/C++Java-In-Place-Swap


**C++代码：**
``` c++
class Solution {
public:
    vector<int> sortArrayByParity(vector<int> A) {
        for (int i = 0, j = 0; j < A.size(); j++)
            if (A[j] % 2 == 0) swap(A[i++], A[j]);
        return A;
    }
};
```

### 数字奇偶性判定：
```
数字num
1. num % 2 == 0（偶数）； num % 2 != 0（奇数）
2. num & 1 == 0（偶数）； num & 1 != 0（奇数）
3. n & (n-1) == 0 （偶数）；  n & (n-1) ！= 0 （奇数）
```

[1]: https://leetcode.com/problems/sort-array-by-parity/description/
