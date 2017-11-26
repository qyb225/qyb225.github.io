---
layout: post
title:  "LeetCode Week9"
date:   2017-10-30 20:30
categories: LeetCode
permalink: /leetcode/week9
---

# Problem Set #9

记录第 9 周 (2017/10/30 - 2017/11/05) 完成的题目。

---

## #16. 3Sum Closest

Difficulty: Medium

**问题描述**：

Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

For example, given array **S = {-1 2 1 -4}**, and **target = 1**.

The sum that is closest to the target is **2**. **(-1 + 2 + 1 = 2)**.


**分析**：

这道题和 #15. 3Sum 题目如出一辙，因此解法相似。[**Go to #15. 3Sum**](#15-3sum)。

**实现**：

C++ 实现：

```cpp
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        if (nums.size() < 3) {
            return 0;
        }
        sort(nums.begin(), nums.end());
        int sum = nums[0] + nums[1] + nums[2];
        int minDistSum = sum;
        int dist = sum > target ? sum - target : target - sum;
        int minDist = dist;
        for (int first = 0; first < nums.size() - 2; ++first) {
            int second = first + 1;
            int third = nums.size() - 1;
            while (second < third) {
                sum = nums[first] + nums[second] + nums[third];
                if (sum > target) {
                    --third;
                    dist = sum - target;
                } else if (sum < target) {
                    ++second;
                    dist = target - sum;
                } else {
                    return target;
                }
                if (dist < minDist) {
                    minDist = dist;
                    minDistSum = sum;
                }
            }
        }
        return minDistSum;
    }
};
```
