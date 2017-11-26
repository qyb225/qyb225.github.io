---
layout: post
title:  "LeetCode Week1"
date:   2017-09-09 20:30
categories: LeetCode
permalink: /leetcode/week1
---

# Problem Set #1

记录第一周 (2017/09/04 - 2017/09/10) 完成的题目。

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
