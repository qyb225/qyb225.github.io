---
layout: post
title:  "LeetCode Week15"
date:   2017-12-11 20:30
categories: LeetCode
permalink: /leetcode/week15
---

# Problem Set #15

记录第 15 周 (2017/12/11 - 2017/12/17) 完成的题目。

---

## #14. Longest Common Prefix

Difficulty: Easy

**问题描述**：

Write a function to find the longest common prefix string amongst an array of strings.

**分析**：

这道题比较容易，只需要两重循环遍历数组列表，判断他们相同 index 的字符是否相等即可，到 '\0' 或者第一个不相等元素则停下。

**实现**：

C 实现：

```c
char* longestCommonPrefix(char** strs, int strsSize) {
    if (strsSize < 1) {
        return "";
    }
    int i = 0, j;
    while (1) {
        int isEqual = 1;
        for (j = 0; j < strsSize - 1; ++j) {
            isEqual = isEqual && strs[j][i] == strs[j + 1][i];
        }
        if (!isEqual || strs[0][i] == '\0') {
            strs[0][i] = '\0';
            char* ans = (char*) malloc(i + 1);
            strcpy(ans, strs[0]);
            return ans;
        }
        ++i;
    }
}
```

