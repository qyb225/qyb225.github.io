---
layout: post
title:  "LeetCode Week11"
date:   2017-11-13 20:30
categories: LeetCode
permalink: /leetcode/week11
---

# Problem Set #11

记录第 11 周 (2017/11/13 - 2017/11/19) 完成的题目。

---

## #20. Valid Parentheses

Difficulty: Medium

**问题描述**：

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, **"()" and "()[]{}"** are all **valid** but **"(]" and "([)]"** are **not**.

**分析**：

**Stack** 应用的经典问题，如果遇到**左括号**则直接入栈，如果遇到**右括号**则匹配，匹配成功的左括号出栈，匹配不成功或最后栈不为空则证明无法匹配，返回 **false**

**实现**：

C 实现：

```c
int isValid(char* s) {
    int lenth = strlen(s);
    char* stackBottom = (char*) malloc(lenth);
    int i, stackTopPtr = -1;
    for (i = 0; i < lenth; ++i) {
        if (s[i] == '(' || s[i] == '[' || s[i] == '{') {
            stackBottom[++stackTopPtr] = s[i];
        } else if ((s[i] == ')' && stackBottom[stackTopPtr] == '(')
                    || (s[i] == ']' && stackBottom[stackTopPtr] == '[')
                    || (s[i] == '}' && stackBottom[stackTopPtr] == '{')) {
            --stackTopPtr;
        } else {
            free(stackBottom);
            return 0;
        }
    }
    free(stackBottom);
    return stackTopPtr == -1;
}
```
