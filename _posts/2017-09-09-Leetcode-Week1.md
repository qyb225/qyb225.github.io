---
layout: post
title:  "LeetCode Week1"
date:   2017-09-09 20:30
categories: LeetCode
permalink: /leetcode/week1
---

# Problem Set #1

记录第一周 (2017/09/04 - 2017/09/10) 完成的题目。

---

##  #50. Pow(x, n)

### 问题描述

Implement pow(x, n).

### 分析

如果采用 x 连乘 n 次的方法解题，复杂度达到 **O(n)** 难以满足要求，因此采用上课所讲的分治法，将问题转化为两个 pow(x, n / 2)相乘，然后递归调用直到 n 为 0 或 1，可以完成该题目，复杂度 **O(logn)**

### 实现

C 语言实现：

```c
double myPow(double x, int n) {
    if (0 > n) {
        return 1 / myPow(x, -n);
    }
    if (0 == n) {
        return 1;
    }
    if (1 == n) {
        return x;
    }
    double temp = myPow(x, n >> 1);
    return temp * temp * (n % 2 ? x : 1);
}
```

---