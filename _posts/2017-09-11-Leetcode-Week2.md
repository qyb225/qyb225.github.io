---
layout: post
title:  "LeetCode Week2"
date:   2017-09-11 17:30
categories: LeetCode
permalink: /leetcode/week2
---

# Problem Set #2

记录第二周 (2017/09/11 - 2017/09/17) 完成的题目。

---

## #8. String to Integer (atoi)

Difficulty: Medium

### 问题描述

Implement atoi to convert a string to an integer.

**Hint**: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

### 分析

题目本身不难，但是通过测试样例发现了以下几个坑：

* 首位可以是正负号
* 开头可以有任意多的 Space ' '
* 计算到第一个非数字字符为止
* 烦人的 Overflow 问题

### 实现

C 语言实现：

```c
int myAtoi(char* str) {
    int n = strlen(str);
    int sign = 1, i = 0;
    long long ans = 0;
    while (str[i] == ' ') {
        ++i;
    }
    if (str[i] == '-') {
        ++i;
        sign = -1;
    } else if (str[i] == '+') {
        ++i;
    }
    while (str[i] <= '9' && str[i] >= '0') {
        ans = 10 * ans + (str[i++] - '0');
        
        /*Overflow*/
        if (ans > INT_MAX) {
            return sign > 0 ? INT_MAX : INT_MIN;
        }
    }
    return sign * ans;
}
```

---

## #9. Palindrome Number

Difficulty: Easy

### 问题描述

Determine whether an integer is a palindrome. Do this without extra space.

### 分析

一开始的想法很简单，int 转换为字符串然后直接递归判断是否回文即可。然后仔细一看题目有一句：**Do this without extra space**. 而转换字符串需要新的的空间开销。然后再的想法就是倒转这个 int，如果倒转后的 reverse_x 和 x 相等，便是回文数。

### 实现

C 实现：

```c
int isPalindrome(int x) {
    if (x < 0) {
        return 0;
    }
    int reverseTemp = x;
    int reverse = 0;
    while (reverseTemp) {
        reverse = 10 * reverse + reverseTemp % 10;
        reverseTemp /= 10;
    }
    return reverse == x;
}
```

---

## #11. Container With Most Water

Difficulty: Medium

### 问题描述

Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note**: You may not slant the container and n is at least 2.

### 分析

看到这个题，第一个想到的办法就是 i, j 分别指向一个柱子（vertical line），然后循环两遍求出所有的面积即可算出最大面积。但是这样的解法复杂度达到 **O(n^2)**，略高。

这个题目开始觉得比较坑的原因是要求解的柱子不是相邻的柱子，很有可能跨很远。不过后来这一点也成了一个突破口，于是最后查阅一些资料后采用下述算法：

* 开始将一个指针 i 指向头，一个指针 j 指向尾部。
* 比较 i, j 的大小，如果 i 小则增加，j 小则减小，因为在 i++ 和 j--的时候横向距离已经小了，我们只能求助于更长的柱子才可能有突破。
* 每次都保存一个 max 值比较新的值，必要时更新 max。

采用这种算法，只需要遍历 height 数组一次，复杂度为 **O(n)**.

---

### 实现

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

---