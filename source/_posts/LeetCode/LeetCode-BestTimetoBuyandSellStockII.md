---
title: LeetCode 122. Best Time to Buy and Sell Stock II
date: 2017-11-21 10:30:50
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - DP
 - Dynamic Programming
---

## [题目描述][1]：
Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

## 题目大意：
假设有一个数组，它的第i个元素是一个给定的股票在第i天的价格。设计一个算法来找到最大的利润。你可以完成尽可能多的交易(多次买卖股票)。然而,你不能同时参与多个交易(你必须在再次购买前出售股票)。
给出一个数组样例[2,1,2,0,1], 返回 2

### 解题思路：
---
这个是一道DP(动态规划)的题。总的来说就是如何N天的总利润如何最大呢？就是一旦有获利空间就去赚钱，所以就是把每两天的价格增长量加起来就是总利润了，如果存在两天的价格增长量为负，那么就不进行交易。同一天买卖，收益为0，所以需要隔天买卖，保证利润增值。
参考LeetCode Discuss：
https://discuss.leetcode.com/topic/17081/three-lines-in-c-with-explanation

**C++代码：**
``` c++
class Solution {
public:
    int maxProfit(vector<int> &prices)
    {
        int ret = 0;
        for (size_t p = 1; p < prices.size(); ++p)
          // Only consider positive value to make sure get max profit
          ret += max(prices[p] - prices[p - 1], 0); 
        return ret;
    }
};
```

**代码分析：**
    1. 题意为给定一个数组，数组中第i个元素的值对应着第i天的股票，你可以完成多次交易，但是每次交易只能买入一次并卖出,求进行
    多次交易所能得到的最大利润。该题为Best Time to Buy and Sell Stock的加强版。
    2. 与Best Time to Buy and Sell Stock类似，该题同样考查的是最大差值。只不过该题考查数组中所有相邻且递增元素
    的数值之差的总和。只要第i+1天的值大于第i天的值，则可买入，求得利润（差值），遍历整个数组，得到所用差值之和即为总的利润。
    负值则跳过。充分体现了DP的精髓，局部最优就是整体最优解。
    3. 假设有四个数字，"a <= b <= c <= d", the profit is "d - a = (b - a) + (c - b) + (d - c)"。
    例如[5,8,9,10],maxProfit=(8-5)+(9-8)+(10-9)=10-5=5; // 这是理想情况
    假如其中有负值，则跳过这几个元素，直到获取正值，保证利润都是递增。


[1]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/
