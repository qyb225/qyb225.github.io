---
layout: post
title:  "LeetCode Week10"
date:   2017-11-06 20:30
categories: LeetCode
permalink: /leetcode/week10
---

# Problem Set #10

记录第 10 周 (2017/11/06 - 2017/11/12) 完成的题目。


---

## #17. Letter Combinations of a Phone Number

Difficulty: Medium

**问题描述**：

Given a digit string, return all possible letter combinations that the number could represent.

Input:Digit string **"23"**

Output: **["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]**.

**分析**：

首先，如果只输入一个数字 **"2"**，则解是 **["a", "b", "c"]**。如果继续输入 **"23"**，则：

* a: a**d**, a**e**, a**f**
* b: b**d**, b**e**, b**f**
* c: c**d**, c**e**, c**f**

**即原来的答案分别加上新来的所有字母**。

设已经算出一个答案 ans，再加入一个新的数字对应的字符集 newCome。算出他俩合并后的答案，用伪代码表示：

```
for i = 0 to ans.length - 1:
    for j = 0 to newCome.lenth -1:
        newAns.append(ans[i] + newCome[j])
    endfor
endfor
```

**实现**：

C++ 实现：

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits);
    vector<string> multiply(vector<string>& strArray, string str);
};

vector<string> Solution::letterCombinations(string digits) {
    string letter[10] = {"", "", "abc", "def", "ghi", 
                         "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> ans;
    for (int i = 0; i < digits.size(); ++i) {
        ans = multiply(ans, letter[digits[i] - '0']);
    }
    return ans;
}

vector<string> Solution::multiply(vector<string>& strArray, string str) {
    vector<string> ans;
    for (int i = 0; i < str.size(); ++i) {
        if (strArray.size()) {
            for (int j = 0; j < strArray.size(); ++j) {
                ans.push_back(strArray[j] + str[i]);
            }
        } else {
            ans.push_back(string(1, str[i]));
        }
    }
    return ans;
}
```
