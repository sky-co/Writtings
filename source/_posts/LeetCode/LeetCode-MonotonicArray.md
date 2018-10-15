---
title: LeetCode 896. Monotonic Array
date: 2018-10-10 09:40:59
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
An array is monotonic if it is either monotone increasing or monotone decreasing.

An array A is monotone increasing if for all i <= j, A[i] <= A[j].  An array A is monotone decreasing if for all i <= j, A[i] >= A[j].

Return true if and only if the given array A is monotonic.

**Example 1:**
```
Input: [1,2,2,3]
Output: true
```
**Example 2:**
```
Input: [6,5,4,4]
Output: true
```
**Example 3:**
```
Input: [1,3,2]
Output: false
```
**Example 4:**
```
Input: [1,2,4,5]
Output: true
```
**Example 5:**
```
Input: [1,1,1]
Output: true
```
**Note:**
```
1 <= A.length <= 50000
-100000 <= A[i] <= 100000
```

## 题目大意：
一个单调数组的定义就是数组元素单调递增或者单调递减。
测试样例见题目描述

### 解题思路：
对于n个元素数组来说判断是否递增/减的方法就是遍历一遍数组元素看看是递增还是递减。
A[i] >= A[i - 1]; // 递增          A[i] <= A[i - 1]; // 递减

---
参考LeetCode Discuss：
https://leetcode.com/problems/monotonic-array/discuss/165899/1-liner-C++


**C++代码：**
``` c++
class Solution {
public:
    bool isMonotonic(vector<int>& A) {
        int size = A.size();
        if ( A[size - 1] > A[0]) {
            for (int i = 0; i < size - 1; ++i) {
                if (A[i + 1] - A[i] < 0) return false;
            }
            return true;
        }
        else {
            for (int i = 0; i < size - 1; ++i) {
                if (A[i] - A[i + 1] < 0) return false;
            }
            return true;
        }
    }
};
```


[1]: https://leetcode.com/problems/monotonic-array
