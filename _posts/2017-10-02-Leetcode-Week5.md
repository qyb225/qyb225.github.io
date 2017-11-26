---
layout: post
title:  "LeetCode Week5"
date:   2017-10-02 20:30
categories: LeetCode
permalink: /leetcode/week5
---


# Problem Set #5

记录第 5 周 (2017/10/02 - 2017/10/08) 完成的题目。

---

## #6. ZigZag Conversion

Difficulty: Medium

**问题描述**

The string **"PAYPALISHIRING"** is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: **"PAHNAPLSIIGYIR"**

Write the code that will take a string and make this conversion given a number of rows

**分析**

直接根据题目表述构建矩阵，按照规则添加元素，然后一层一层导入最后的 ans 就能够解出此题，且运行效率也能让人满意，只需要遍历一遍字符串即可，复杂度 **O(n)**。

**实现**

C++ 实现：

```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        string* matrix = new string[numRows];
        int i = 0;
        while (i < s.size()) {
            int j = 0;
            while (j < numRows && i < s.size()) {
                matrix[j++] += s[i++];
            }
            j -= 2;
            while (j > 0 && i < s.size()) {
                matrix[j--] += s[i++];
            }
        }
        string ans = "";
        for (int i = 0; i < numRows; ++i) {
            ans += matrix[i];
        }
        delete[] matrix;
        return ans;
    }
};
```

---