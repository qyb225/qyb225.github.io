---
layout: post
title:  "LeetCode Week4"
date:   2017-09-26 20:30
categories: LeetCode
permalink: /leetcode/week4
---

# Problem Set #4

记录第 4 周 (2017/09/25 - 2017/10/01) 完成的题目。


## #3. Longest Substring Without Repeating Characters

Difficulty: Medium

**问题描述**

Given a string, find the length of the longest substring without repeating characters.

Examples:

Given **"abcabcbb"**, the answer is **"abc"**, which the length is **3**.

Given **"bbbbb"**, the answer is **"b"**, with the length of **1**.  

Given **"pwwkew"**, the answer is "wke", with the length of **3**.


**分析**

首先需要一个数组，存储所有的字串是否出现过。指针 i 和指针 j 分别指向第一二个元素。j 遍历整个字符串，如果 j 没有出现过，则自增。如果发生了重复，则 i 前移到重复元素后一个元素并消去途中所有元素的 exist 值。

复杂度分析**：j 会遍历字符串，i 在最差情况下也会遍历字符串，字符串最多被遍历2次，所以复杂度为 **O(n)**

**实现**

C 实现：

```c
int lengthOfLongestSubstring(char* s) {
    int i = 0, j = 1;
    if (s[i] == '\0' || s[j] == '\0') {
        return strlen(s);
    }
    int exist[256] = { 0 };
    int count = 1, max = 0;
    exist[s[i]] = 1;
    while (s[j] != '\0') {
        if (!exist[s[j]]) {
            exist[s[j]] = 1;
            if (j - i + 1 > max) {
                max = j - i + 1;
            }
            ++j;
        } else {
            while (s[i] != s[j]) {
                exist[s[i++]] = 0;
            }
            exist[s[i++]] = 0;
        }
    }
    return max;
}
```

---