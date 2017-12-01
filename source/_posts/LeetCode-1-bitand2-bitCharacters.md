---
title: LeetCode 717. 1-bit and 2-bit Characters
date: 2017-12-01 17:22:49
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
LeetCode 717. 1-bit and 2-bit Characters
We have two special characters. The first character can be represented by one bit 0. The second character can be represented by two bits (10 or 11).
Now given a string represented by several bits. Return whether the last character must be a one-bit character or not. The given string will always end with a zero.

**Example 1:**
```
Input:
bits = [1, 0, 0]
Output: True
Explanation:
The only way to decode it is two-bit character and one-bit character. So the last character
is one-bit character.
```
**Example 2:**
``` 
Input:
bits = [1, 1, 1, 0]
Output: False
Explanation:
The only way to decode it is two-bit character and two-bit character. So the last character
is NOT one-bit character.
```

**Note:**
    ``` c++
    1 <= len(bits) <= 1000.
    bits[i] is always 0 or 1.
    ```
## 题目大意：
01序列由三种成分构成：10, 11, 0
求序列经过解析后，最后一个成分是否为0

### 解题思路：
---
这道题就是已知一个字符串（最后一个字符0）和三种构成成分，判定该字符串是否合法。
观察三种字符串可以发现，两bits的只有10和11两种（第一个bit都是1，第二bit随意），一个bit的只有0满足。
转变思路后就是检查一个最后一个bit是0的字符串中是否存在单个bit是1的情况（00可以解析为2个bit的0组成）。

参考LeetCode Discuss：
https://discuss.leetcode.com/topic/108743/java-solution-1-or-2/4

**C++代码：**
``` c++
class Solution {
public:
    bool isOneBitCharacter(vector<int>& bits) {
        int n = bits.size(), i = 0;
        while (i < n-1) i += bits[i]+1; 
        return i == n-1;
    }
};
```

**代码分析：**
此题没有运用一个通用的算法，而是根据题目的特性做出解答，具体步骤就是
    1. 放置一个指针在字符串首位用于遍历
    2. 每次看见0的时候，往前走1步
    3. 每次看见1的时候（可以认为是发现11或者10），往前走2步
    4. 假如出现单个1的时候，指针往前走2步就可能超过字符串的本身长度，这种情况就是不符合题意的case。
    正常遍历是可以最终到达n-1的位置（n为字符串长度）



[1]: https://leetcode.com/problems/1-bit-and-2-bit-characters
