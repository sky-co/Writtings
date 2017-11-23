---
title: LeetCode 121. Best Time to Buy and Sell Stock
date: 2017-11-23 12:11:53
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - Kadane's Algorithm
---

## [题目描述][1]：
Say you have an array for which the ith element is the price of a given stock on day i.
If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

**Example 1:**
```
Input: [7, 1, 5, 3, 6, 4]
Output: 5

max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
```
**Example 2:**
```
Input: [7, 6, 4, 3, 1]
Output: 0

In this case, no transaction is done, i.e. max profit = 0.
```

## 题目大意：
给一个数组prices[]，prices[i]代表股票在第i天的售价，求出只做一次交易(一次买入和卖出)能得到的最大收益。 

### 解题思路1：
---
Brute & Force（直观做法）: 两个循环，逐一找到每两个元素的差值，取最大的即为为maxProfit，时间复杂度O(n^2)

**C++代码：**
``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices)
    {
        int maxProfit = 0;
        for (int i = 0; i < prices.size(); i++) {
            for (int j = i + 1; j < prices.size(); j++) {
                if (0 < prices[j] - prices[i])
                    maxProfit = max(prices[j] - prices[i], maxProfit);
            }
        }
        return maxProfit;
    }
};
```

**代码分析：**
    1. 遍历数组，找到MaxProfit（效率较低）
    2. 保证每一次比较的差值都是正值即可

### 解题思路2：
---
根据题目意思，只需要找出一次交易的最大的差值。对于此题就是要找到最小值（下限）和最大值（上限）。
只要能固定住其中之一，就可以通过一次遍历，时间复杂度O(n)，找到maxProfit.
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/5863/sharing-my-simple-and-clear-c-solution/2
https://discuss.leetcode.com/topic/2763/a-o-1-n-solution

**C++代码：**
``` c++
class Solution {
public:
    int maxProfit(vector<int>& prices)
    {
        int maxProfit = 0;
        int minPrice = INT_MAX;
        for (int i = 0; i < prices.size(); i++) {
            minPrice = min(minPrice, prices[i]);
            maxProfit = max(maxProfit, prices[i] - minPrice);
        }
        return maxProfit;
    }
};
```

**代码分析：**
    1. 将如何获得maxProfit的问题转化为：取出maxProfit和prices[i] - minPrice之间的最大差值
    2. 通过观察发现并不是每一次迭代都需要去计算minPrice，可以优化为以下代码
    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices)
        {
            int maxProfit = 0;
            int minPrice = INT_MAX;
            for (int i = 0; i < prices.size(); i++) {
                if (prices[i] < minPrice)
                    minPrice = min(minPrice , prices[i]);
                maxProfit = max(maxProfit, prices[i] - minPrice);
            }
            return maxProfit;
        }
    };
    ```

**拓展:**
    [Kadane's Algorithm][2]


[1]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/
[2]: https://discuss.leetcode.com/topic/19853/kadane-s-algorithm-since-no-one-has-mentioned-about-this-so-far-in-case-if-interviewer-twists-the-input/5
