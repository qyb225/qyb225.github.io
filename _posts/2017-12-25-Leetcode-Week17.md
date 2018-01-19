---
layout: post
title:  "LeetCode Week17"
date:   2017-12-25 20:30
categories: LeetCode
permalink: /leetcode/week17
---

# Problem Set #17

记录第 17 周 (2017/12/25 - 2017/12/31) 完成的题目。

---


## #169. Majority Element

Difficulty: Medium

**问题描述**：

Given an array of size n, find the majority element. The majority element is the element that appears more than **n / 2** times.

You may assume that the array is non-empty and the majority element always exist in the array.

**分析**：

如果 majority number 出现了 n / 2 次以上，那么其它的 number 的数量总和也不会有它多。

随机选择一个 number 作为 majority，一个计数器 count。遍历整个 array，如果 array[i] == majority，那么我们让 count + 1。

如果不相等则检查 count 是否为 0， 如果为 0 则重新选择当前的 array[i] 为新的 majority。

如果 count 仍然 > 0，自减 count 即可。

**实现**：

C 实现：

```c
int majorityElement(int* nums, int numsSize) {
    int ans = nums[0];
    int i, count = 0;
    for (i = 0; i < numsSize; ++i) {
        if (ans == nums[i]) {
            ++count;
        } else if (count == 0) {
            ans = nums[i];
        } else {
            --count;
        }
    }
    return ans;
}
```
---
