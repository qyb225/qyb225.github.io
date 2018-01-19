---
layout: post
title:  "LeetCode Week19"
date:   2018-01-08 20:30
categories: LeetCode
permalink: /leetcode/week19
---

# Problem Set #19

记录第 19 周 (2018/01/08 - 2017/01/14) 完成的题目。

---

## #55. Jump Game

Difficulty: Medium

**问题描述**：

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**A = [2,3,1,1,4]**, return **true**.

**A = [3,2,1,0,4]**, return **false**.

**分析**：

我们先假设如果不能达到会发生什么，也就是其中有一个位置，让前面的位置跨越最大的 step 都达不到。于是我想到一个思路，逆序遍历整个序列。

一个点和自己的距离为 0，设置一个 count，每次向前走一步，count++。如果向前走的那一步的点可以跳到这个位置，那么count 置为 0，意思是从当前点开始，继续寻找前方的点。 整个算法只需要扫描一趟，复杂度 **O(n)**

**实现**：

C 实现：

```c
int canJump(int* nums, int numsSize) {
    if (numsSize < 2) {
        return 1;
    }
    int i, count = 0;
    for (i = numsSize - 1; i >= 0; --i) {
        if (nums[i] >= count) {
            count = 0;
        }
        ++count;
    }
    return count == 1;
}
```
