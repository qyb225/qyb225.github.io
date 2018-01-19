---
layout: post
title:  "LeetCode Week14"
date:   2017-12-04 20:30
categories: LeetCode
permalink: /leetcode/week14
---

# Problem Set #14

记录第 14 周 (2017/12/04 - 2017/12/11) 完成的题目。

---

## #70. Climbing Stairs

Difficulty: Easy

**问题描述**：

You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

Note: Given n will be a positive integer.

**分析**：

设 dp[n] 为 n 阶楼梯的走法。因为每一阶楼梯都可以由一阶前的楼梯走一步上来，也能又前两阶的楼梯一步跨两阶到达，因此： **dp[i] = dp[i - 1] + dp[i - 2]**.

**实现**：

C++ 实现：

```cpp
class Solution {
public:
    int climbStairs(int n) {
        vector<int> dp(n, 0);
        dp[0] = 1;
        dp[1] = 2;

        int i = 2;
        while (i < n) {
            dp[i] = dp[i - 1] + dp[i - 2];
            ++i;
        }

        return dp[n - 1];
    }
};
```
