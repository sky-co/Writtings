---
title: LeetCode 697. Degree of an Array
date: 2017-11-15 16:10:07
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
---

## [题目描述][1]：
Given a non-empty array of non-negative integers nums, the degree of this array is defined as the maximum frequency of any one of its elements.
Your task is to find the smallest possible length of a (contiguous) subarray of nums, that has the same degree as nums.
**Example 1:**
```
Input: [1, 2, 2, 3, 1]
Output: 2
Explanation:
    The input array has a degree of 2 because both elements 1 and 2 appear twice.
    Of the subarrays that have the same degree:
    [1, 2, 2, 3, 1], [1, 2, 2, 3], [2, 2, 3, 1], [1, 2, 2], [2, 2, 3], [2, 2]
    The shortest length is 2. So return 2.
```
**Example 2:**
```
Input: [1,2,2,3,1,4,2]
Output: 6
```
**Note:**
    - nums.length will be between 1 and 50,000.
    - nums[i] will be an integer between 0 and 49,999.

## 题目大意：
给定非空非负整数数组，数组的度是指元素的最大出现次数。
寻找最大连续区间，使得区间的度与原数组的度相同。

### 解题思路1：
---
数组mapCnt记录元素的出现个数
数组mapMax记录元素的最大degree
startIdx和endIdx记录元素的最小和最大下标（length = endIdx - startIdx + 1）
遍历即可
参考LeetCode Discuss：https://discuss.leetcode.com/topic/107102/my-accepted-c-solution-o-n/3

**C++代码：**
``` c++
    int findShortestSubArray(vector<int>& nums)
    {
        map<int, int> mapCnt;
        int max = 0;
        int min = 0;
        if (true == doFineSubArray(nums, mapCnt)) {
            map<int,int>::iterator it;
            for (it = mapCnt.begin(); it != mapCnt.end(); ++it) {
                if (it->second > max) max = it->second;
            }

            map<int, int> mapMax;
            for (it = mapCnt.begin(); it != mapCnt.end(); ++it) {
                if (it->second == max) mapMax.insert(pair<int , int>(it->first, it->second));
            }

            max = 0;
            for (it = mapMax.begin(); it != mapMax.end(); ++it) {
                int beginIdx = 0;
                for (int idx = 0; idx < nums.size(); idx++) {
                    if (nums[idx] == it->first) {
                        beginIdx = idx;
                        break;
                    }
                }

                int endIdx = 0;
                for (int idx = nums.size() - 1; 0 <= idx; idx--) {
                    if (nums[idx] == it->first) {
                        endIdx = idx;
                        break;
                    }
                }
                int length = endIdx - beginIdx + 1;
                if (0 == min) min = length;
                else min = (min < length) ? min : length;
            }
        }
        return min;
    }

    bool doFineSubArray(vector<int>& nums, map<int, int>& mapCnt)
    {
        for (int idx = 0; idx < nums.size(); idx++) {
            if (0 != mapCnt.count(nums[idx])) ++mapCnt[nums[idx]];
            else mapCnt.insert(pair<int , int>(nums[idx], 1));
        }

        if (0 < mapCnt.size()) return true;
        else return true;
    }
```

**代码分析：**

    1. 这中解法如果出现nums={1, 2, 3}时候，最后的循环遍历时间复杂度会变成0(n^2)
    2. std::map的本质是红黑树，查找的时间复杂度是O(nlogn).
    3. C++中的hashtable，可以考虑unordered_map（C++11/14）.

### 解题思路2：
---
数组unordered_map<int,vector<int>> mapCnt用来记录数值和在数组nums中出现的下标
max找到最大的degree
min找到最短的subarray的length
遍历即可
参考LeetCode Discuss：https://discuss.leetcode.com/topic/107162/straightforward-c-solution-35ms

**C++代码：**
``` c++
    int findShortestSubArray(vector<int>& nums)
    {
        unordered_map<int,vector<int>> mapCnt;
        for (int idx = 0; idx < nums.size(); idx++) mapCnt[nums[idx]].push_back(idx);
        int degree = 0;
        for (auto it = mapCnt.begin(); it != mapCnt.end(); it++)
            degree = max(degree, static_cast<int>(it->second.size()));
        int shortest = nums.size();
        for (auto it = mapCnt.begin(); it != mapCnt.end(); ++it) {
            if (degree == it->second.size()) {
                shortest = min(shortest, static_cast<int>(it->second.back() - it->second[0] + 1));
            }
        }
        return shortest;
    }
```

**代码分析：**
    1. 这中解法如果出现nums={1, 2, 3}时候，最后的循环遍历时间复杂度会变成0(n^2)
    2. unordered_map（C++11/14）本质是hashtable，可以实现访问时间O(1)的时间复杂度.
    3. 第二个元素vector<int>用来存储元素出现的下标，同时该数组长度也是元素出现的个数，节省了上一个
        算法的计数步骤，关键是，最后计算subarray的length的时候，收尾元素的差值+1就是shortest，灰常好使。


### 解题思路3：
---
使用两个数组unordered_map<int,int>
    数组startIndex用来记录每个元素在nums中的起始index
    数组count用来记录每个元素在nums中出现的degree
遍历即可，更新最大的frequency在遍历时，如果多个元素有相同的frequency，比较他们的长度，选择subarray的length最短那个
参考LeetCode Discuss：https://discuss.leetcode.com/topic/107162/straightforward-c-solution-35ms

**C++代码：**
``` c++
    int findShortestSubArray(vector<int>& nums)
    {
        unordered_map<int,vector<int>> mp;
        for(int i=0;i<nums.size();i++) mp[nums[i]].push_back(i);
        int degree=0;
        for(auto it=mp.begin();it!=mp.end();it++) degree=max(degree,int(it->second.size()));
        int shortest=nums.size();
        for(auto it=mp.begin();it!=mp.end();it++)
        {
            if(it->second.size()==degree)
            {
                shortest=min(shortest,it->second.back()-it->second[0]+1);
            }
        }
        return shortest;
    }
```

**代码分析：**
    1. 一个循环中用来记录元素的起始位置，并且更新元素的degree，同时计算出当前的最小len
    2. 如果两个元素拥有相同的degree，比较并取length最小的那个
    3. 遍历nums中每个元素的同时，如果元素的出现次数frequency大于当前fre，更新len和fre
    4. len的长度改变只有两个时候：两个元素的degree相同和元素的出现次数frequ>当前fre时候


[1]: https://leetcode.com/problems/degree-of-an-array/description/
