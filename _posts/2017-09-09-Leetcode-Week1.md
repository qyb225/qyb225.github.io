---
layout: post
title:  "LeetCode Week1"
date:   2017-09-09 20:30
categories: LeetCode
permalink: /leetcode/week1
---

# Problem Set #1

记录第一周 (2017/09/04 - 2017/09/10) 完成的题目。本周共完成 5 题。

---

## #2. Add Two Numbers

**问题描述**

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)**

**Output: 7 -> 0 -> 8**

**分析**

一趟循环，直接将 l1 和 l2 节点相加存入 l1。需要注意的是，如果需要进位而后面没有 ListNode，则需要自己创建一个 ListNode 存入 1。如果一趟循环后，l2 更长，则直接连接 l1 目前的位置 和 l2 目前的位置即可。

**实现**

C++ 实现：

```cpp
//Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
 
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2);
};

ListNode* Solution::addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode* ans = l1;
    ListNode* l1FrontNode = NULL;
    while (l1 && l2) {
        l1->val += l2->val;
        /*保存前一个节点*/
        l1FrontNode = l1;
        l1 = l1->next;
        l2 = l2->next;
        if (l1FrontNode->val > 9) {
            l1FrontNode->val -= 10;
            if (l1) {
                l1->val += 1;
            } else {
                l1 = l1FrontNode->next = new ListNode(1);
            }
        }
    }
    /*如果 l2 已经为空，则链表后半部分不变，只需要处理进位的问题*/
    if (l1) {
        while (l1 && l1->val > 9) {
            l1->val -= 10;
            if (l1->next) {
                l1->next->val += 1;
            } else {
                l1->next = new ListNode(1);
            }
            l1 = l1->next;
        }
    }
    /*如果l1已经空了，我们需要将l1后侧链接到l2的位置*/
    else {
        l1FrontNode->next = l2;
    }
    return ans;
}

```

---

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

## #7. Reverse Integer

Difficulty: Easy

**问题描述**

Reverse digits of an integer.

Example1: **x = 123, return 321**  
Example2: **x = -123, return -321**

**分析**

每次 x % 10 获得个位，然后再 x / 10，依次从后向前取到了每一位数字，不断将之前 ans * 10 + 这个数字就好，值得注意的是溢出问题，用了 long long 存储并和 INT_MAX 比较，虽然说这是一个取巧的方法，并没有真正的解决溢出问题，但是暂时也想不到什么更好的方法了。

**实现**

C 实现：

```c
int reverse(int x) {
    long long ans = 0;
    int sign = 1;
    int num;
    if (x < 0) {
        x = -x;
        sign = -1;
    }
    while (x > 0) {
        num = x % 10;
        x /= 10;
        ans = ans * 10 + num;
        if (ans > INT_MAX) {
            return 0;
        }
    }
    
    return sign * ans;
}
```

---

##  #50. Pow(x, n)

Difficulty: Medium

**问题描述**

Implement pow(x, n).

**分析**

如果采用 x 连乘 n 次的方法解题，复杂度达到 **O(n)** 难以满足要求，因此采用上课所讲的分治法，将问题转化为两个 pow(x, n / 2)相乘，然后递归调用直到 n 为 0 或 1，可以完成该题目，复杂度 **O(logn)**

**实现**

C 实现：

```c
double myPow(double x, int n) {
    if (0 > n) {
        return 1 / myPow(x, -n);
    }
    if (0 == n) {
        return 1;
    }
    if (1 == n) {
        return x;
    }
    double temp = myPow(x, n >> 1);
    return temp * temp * (n % 2 ? x : 1);
}
```

---