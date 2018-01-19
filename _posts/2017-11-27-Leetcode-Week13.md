---
layout: post
title:  "LeetCode Week13"
date:   2017-11-27 20:30
categories: LeetCode
permalink: /leetcode/week13
---

# Problem Set #13

记录第 13 周 (2017/11/27 - 2017/12/03) 完成的题目。

---

## #72. Edit Distance

Difficulty: Hard

**问题描述**：

Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

a) Insert a character
b) Delete a character
c) Replace a character

**分析**：

设 dp[i][j] 为 str1(0 : i) 和 str2(0 : j) 的最小编辑距离，那么分为 3 种情况：

* "abc" -> "abcd"

这个时候只需要增加一个符号即可，因此 **dp[i][j] = dp[i - 1][j] + 1**.

* "abc" -> "ab"

这个时候需要减少一个符号，因此 **dp[i][j] = dp[i][j - 1] + 1**.

* “abc” -> "abd"

这个时候需要改变一个符号，因此 **dp[i][j] = dp[i - 1][j - 1] + 1**.

**实现**：

C++ 实现：

```cpp
class Solution {
public:
    int min(int a, int b, int c) {
        int temp;
        if (a < b) {
            temp = a;
        } else {
            temp = b;
        }
        return (c < temp ? c : temp);
    }
    int minDistance(string word1, string word2) {
        std::vector<std::vector<int>> dp(word1.size() + 1, std::vector<int>(word2.size() + 1));
        for (int i = 0; i <= word1.size(); ++i) {
            dp[i][0] = i;
        }
        for (int i = 0; i <= word2.size(); ++i) {
            dp[0][i] = i;
        }
        for (int i = 1; i <= word1.size(); ++i) {
            for (int j = 1; j <= word2.size(); ++j) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = min(dp[i - 1][j] + 1,
                                   dp[i][j - 1] + 1,
                                   dp[i - 1][j - 1] + 1);
                }
            }
        }
        return dp[word1.size()][word2.size()];
    }
};
```
