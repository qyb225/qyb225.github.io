---
layout: post
title:  "LeetCode Week7"
date:   2017-10-16 20:30
categories: LeetCode
permalink: /leetcode/week7
---

# Problem Set #7

记录第 7 周 (2017/10/16 - 2017/10/22) 完成的题目。

---


## #8. String to Integer (atoi)

Difficulty: Medium

**问题描述**：

Implement atoi to convert a string to an integer.

**Hint**: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

**分析**：

题目本身不难，但是通过测试样例发现了以下几个坑：

* 首位可以是正负号
* 开头可以有任意多的 Space ' '
* 计算到第一个非数字字符为止
* 烦人的 Overflow 问题

**实现**：

C 语言实现：

```c
int myAtoi(char* str) {
    int n = strlen(str);
    int sign = 1, i = 0;
    long ans = 0;
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
