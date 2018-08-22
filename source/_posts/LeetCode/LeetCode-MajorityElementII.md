---
title: LeetCode 169. Majority Element II
date: 2017-11-28 11:42:33
categories: LeetCode
tags:
 - LeetCode
 - Algorithms
 - Data Structure
 - Array
 - Majority Element
 - Moore’s voting algorithm
---

## [题目描述][1]：
Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times. The algorithm should run in linear time and in O(1) space.
Hint:
How many majority elements could it possibly have?

## 题目大意：
给定一个大小为n的整数数组，从中找出所有出现次数超过 ⌊ n/3 ⌋ 的元素。算法应该满足线性时间复杂度和O(1)空间复杂度。
提示：
一共可能有多少个“众数”？

### 解题思路：
---
可以从[Moore’s voting algorithm][2]中得到一些启发

参考LeetCode Discuss：
https://leetcode.com/discuss/43248/boyer-moore-majority-vote-algorithm-and-my-elaboration

观察可知，数组中至多可能会有2个出现次数超过 ⌊ n/3 ⌋ 的众数
记变量n1, n2为候选众数； c1, c2为它们对应的出现次数
遍历数组，记当前数字为num
若num与n1或n2相同，则将其对应的出现次数加1
否则，若c1或c2为0，则将其置为1，对应的候选众数置为num
否则，将c1与c2分别减1
最后，再统计一次候选众数在数组中出现的次数，若满足要求，则返回之。

**C++代码：**
``` c++
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        vector<int> majorList;
        // first round to find the candidates
        int count1 = 0;
        int candidate1 = 0;
        int count2 = 0;
        int candidate2 = 0;
        for (int i = 0; i < nums.size(); i++) {
             // every iteration deal with one possibility
            if (candidate1 == nums[i]) count1++;
            else if (candidate2 == nums[i]) count2++;
            else if (0 == count1) {
                count1 = 1;
                candidate1 = nums[i];
            }
            else if (0 == count2) {
                count2 = 1;
                candidate2 = nums[i];
            }
            else {
                count1--;
                count2--;
            }
        }
        // second round to check
        // notice that num1 and num2 can be the same, but in the following we only count once!
        count1 = count2 = 0;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] == candidate1) count1++;
            else if (nums[i] == candidate2) count2++;
        }
        if (nums.size()/3 < count1)
            majorList.push_back(candidate1);
        if (nums.size()/3 < count2)
            majorList.push_back(candidate2);

        return majorList;
    }
};
```

**代码分析：**
此题是[LeetCode 169. Majority Element][3]的加强版，同时要找出大于n/3的众数，难点在于
    1. 时间复杂度O(n)，空间复杂度O(1) -- 注定只能遍历元素，并且用单个变量来完成计数
    2. 众数的条件是n/3

针对此题的n/3的进行深入分析，可以发现有三种情况：
    1. 不存在大于n/3的众数，第一轮结束后，第二轮check时候就会把非众数都过滤掉。
    2. 只存在1个大于n/3的众数, 其余元素个数总数小于2n/3。递减(pair out)时候可以保证众数的count不为0, 第二轮check时候
    就会把非众数都过滤掉。
    3. 存在2个大于n/3的众数，其余元素都小于n/3。递减(pair out)时候可以保证2个众数的count不为0, 第二轮check时候
    就会把非众数都过滤掉。

**Boyer-Moore Majority Vote algorithm**的拓展：
1. https://discuss.leetcode.com/topic/65042/my-understanding-of-boyer-moore-majority-vote
2. https://gregable.com/2013/10/majority-vote-algorithm-find-majority.html



[1]: https://leetcode.com/problems/majority-element-ii
[2]: http://blog.csdn.net/chfe007/article/details/42919017
[3]: http://skyhacks.org/2017/11/24/LeetCode-MajorityElement
