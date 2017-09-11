---
layout: post
title:  "LeetCode Week2"
date:   2017-09-10 17:30
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