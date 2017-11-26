---
layout: post
title:  "LeetCode Week12"
date:   2017-11-20 20:30
categories: LeetCode
permalink: /leetcode/week12
---

# Problem Set #12

记录第 12 周 (2017/11/20 - 2017/11/26) 完成的题目。

---

## #91. Decode Ways

Difficulty: Medium

**问题描述**：

A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1
'B' -> 2
...
'Z' -> 26

Given an encoded message containing digits, determine the total number of ways to decode it.

**For example**

Given encoded message "**12**", it could be decoded as "**AB**" **(1 2)** or "**L**" (**12**).

The number of ways decoding "12" is **2**.

---

**分析**：

设 dp[n] 为字符串从 0 到 n 的可解码方式的总和。

如果当前数字 s[n] 为 ‘1 - 9’，则可以继续正常解码，**dp[n] = dp[n - 1]**。

如果当前数字和之前一位数字 s[n - 1 : n] 为 '10 - 26'，则还可以进行新的一种解码，**dp[n] += dp[n - 2]**。

最终一轮循环到最后一位数字即可得到解码总数，即 dp[N]。

---

**实现**：

```c
int numDecodings(char *s) {
    int n = strlen(s), nWays = 0;
    if (n < 1 || s[0] == '0') {
        return 0;
    }
    int dp[2];
    int canDecode;
    dp[0] = dp[1] = 1;
    for (int i = 1; i < n; ++i) {
        nWays = 0;
        canDecode = 0;
        if (s[i] != '0') {
            nWays += dp[1];
            canDecode = 1;
        }
        if ((s[i] <= '6' && s[i - 1] == '2') || s[i - 1] == '1') {
            nWays += dp[0];
            canDecode = 1;
        }
        if (!canDecode) {
            return 0;
        }
        dp[0] = dp[1];
        dp[1] = nWays;
    }
    return dp[1];
}
```