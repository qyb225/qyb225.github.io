---
layout: post
title:  "LeetCode Week8"
date:   2017-10-23 20:30
categories: LeetCode
permalink: /leetcode/week8
---

# Problem Set #8

记录第 8 周 (2017/10/23 - 2017/10/29) 完成的题目。

---

## #11. Container With Most Water

Difficulty: Medium

**问题描述**：

Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note**: You may not slant the container and n is at least 2.

**分析**：

看到这个题，第一个想到的办法就是 i, j 分别指向一个柱子（vertical line），然后循环两遍求出所有的面积即可算出最大面积。但是这样的解法复杂度达到 **O(n^2)**，略高。

这个题目开始觉得比较坑的原因是要求解的柱子不是相邻的柱子，很有可能跨很远。不过后来这一点也成了一个突破口，于是最后查阅一些资料后采用下述算法：

* 开始将一个指针 i 指向头，一个指针 j 指向尾部。
* 比较 i, j 的大小，如果 i 小则增加，j 小则减小，因为在 i++ 和 j-\-的时候横向距离已经小了，我们只能求助于更长的柱子才可能有突破。
* 每次都保存一个 max 值比较新的值，必要时更新 max。

采用这种算法，只需要遍历 height 数组一次，复杂度为 **O(n)**.

---

**实现**：

C 实现

```c
int maxArea(int* height, int heightSize) {
    int i = 0, j = heightSize -1;
    int maxArea = 0;
    while (i < j) {
        int area;
        if (height[i] < height[j]) {
            area = (j - i) * height[i++];
        } else {
            area = (j - i) * height[j--];
        }
        if (area > maxArea) {
            maxArea = area;
        }
    }
    return maxArea;
}
```
