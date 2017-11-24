---
title: LeetCode 169. Majority Element
date: 2017-11-24 17:27:42
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - Sort
 - HashTable
 - Majority Element
 - Moore’s voting algorithm
---

## [题目描述][1]：
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.
You may assume that the array is non-empty and the majority element always exist in the array.

## 题目大意：
给定一个长度为n的数组，寻找其中的“众数”。众数是指出现次数大于 ⌊ n/2 ⌋ 的元素。
你可以假设数组是非空的并且数组中的众数永远存在。

##  参考LeetCode Discuss：
https://discuss.leetcode.com/topic/8692/o-n-time-o-1-space-fastest-solution/20
https://discuss.leetcode.com/topic/17446/6-suggested-solutions-in-c-with-explanations


### 解题思路1：
---
HashTable：使用HashTasble给每个出现的元素计数，遍历数组，发现超过半数的众数就返回。

**C++代码：**
``` c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int, int> mapNums;
        for (int i = 0; i < nums.size(); ++i) {
            ++mapNums[nums[i]];
            if (mapNums[nums[i]] > nums.size() / 2) return nums[i];
        }
        return 0;
    }
};
```

**代码分析：**
时间复杂度满足O(n)，但是由于HashTable（unordered_map）的存在，空间复杂度就不能满足O(1)。

### 解题思路2：
---
排序：排序的结果就是众数如果存在，中间的元素一定是众数（过半）

**C++代码：**
``` c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return (nums.size() / 2);
    }
};
```

**代码分析：**
1. 时间复杂度主要在排序上， N*log2(N)
2. 如果数组为空或者不存在众数，上述代码需要考虑更全面些
    ```c++
    class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            sort(nums.begin(), nums.end());
            int pivot = nums.size() / 2;
            int count = 0;
            for (int i = pivot; i < nums.size(); i++) {
                if (nums[pivot] != nums[i]) break;
                count++;
            }
            for (int i = pivot - 1; i >= 0; i--) {
                if (nums[pivot] != nums[i]) break;
                count++;
            }
            if (count > pivot) return nums[pivot];
            else return 0;
        }
    };
    ```

### 解题思路3：
---
投票算法：遍历元素，利用candidate 和 count两个变量找到众数。（前提：众数一定存在）

**C++代码：**
``` c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int major = nums, counts = 0, n = nums.size();
        for (int i = 0; i < n; i++) {
            if (!counts) {
                major = nums[i];
                counts = 1;
            }
            else counts += (nums[i] == major) ? 1 : -1;
        }
        return major;
    }
};
```

**代码分析：**
[Moore’s voting algorithm][2]是比较有意思的一个算法，在dicuss上看到的。这个算法是解决这样一个问题：从一个数组中找出出现半数以上的元素。大意是这样：
> We will sweep down the sequence starting at the pointer position shown above. As we sweep we maintain a pair consisting of a current candidate and a counter. Initially, the current candidate is unknown and the counter is 0.
When we move the pointer forward over an element e:
- If the counter is 0, we set the current candidate to e and we set the counter to 1.
- If the counter is not 0, we increment or decrement the counter according to whether e is the current candidate.
>When we are done, the current candidate is the majority element, if there is a majority.

**“投票算法”**
设定两个变量candidate和count。candidate保存当前可能的候选众数，count保存该候选众数的出现次数。
遍历数组num。
如果当前的数字e与候选众数candidate相同，则将计数count + 1
否则，如果当前的候选众数candidate为空，或者count为0，则将候选众数candidate的值置为e，并将计数count置为1。
否则，将计数count - 1
最终留下的候选众数candidate即为最终答案。
以上算法时间复杂度为O(n)，空间复杂度为O(1)

## 官方解析：
时间复杂度: O(n2) — 蛮力法: 依次检查每一个元素是否为众数
时间复杂度: O(n), 空间复杂度: O(n) — 哈希表: 维护一个每一个元素出现次数的哈希表, 然后找到出现次数最多的元素
时间复杂度: O(n log n) — 排序: 在排序后找出连续重复出现次数最多的元素
平均时间复杂度: O(n), 最坏复杂度: 无穷大 — 随机算法: 随机选取一个元素计算其是否为众数. 如果不是, 就重复上一步骤直到找到为止。 由于选出众数的概率 > 1 / 2, 因此期望的尝试次数 < 2
时间复杂度: O(n log n) — 分治法: 将数组拆成2半, 然后找出前一半的众数A和后一半的众数B。则全局众数要么是A要么是B。 如果 A == B, 则它自然而然就是全局众数。 如果不是, 则A和B都是候选众数, 则至多只需要检查这两个元素的出现次数即可。 时间复杂度, T(n) = T(n/2) + 2n = O(n log n).
时间复杂度: O(n) — Moore投票算法: 我们维护一个当前的候选众数和一个初始为0的计数器。遍历数组时，我们看当前的元素x:

    * 如果计数器是0, 我们将候选众数置为 x 并将计数器置为 1
    * 如果计数器非0, 我们根据x与当前的候选众数是否相等对计数器+1或者-1
    * 一趟之后, 当前的候选众数就是所求众数. 时间复杂度 = O(n).

时间复杂度: O(n) — 位操作法: 我们需要32次迭代, 每一次计算所有n个数的第i位的1的个数。由于众数一定存在，那么或者1的个数>0的个数 或者反过来(但绝不会相同)。 众数的第i位一定是计数较多数字。


[1]: https://leetcode.com/problems/majority-element/description/
[2]: http://blog.csdn.net/chfe007/article/details/42919017
