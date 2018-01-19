---
layout: post
title:  "LeetCode Week2"
date:   2017-09-11 17:30
categories: LeetCode
permalink: /leetcode/week2
---

# Problem Set #2

记录第二周 (2017/09/11 - 2017/09/17) 完成的题目。

---

## #15. 3Sum

Difficulty: Medium

**问题描述**：

Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

Note: The solution set must not contain duplicate triplets.

For example: 

given array **S = [-1, 0, 1, 2, -1, -4]**,

A solution set is: **[ [-1, 0, 1], [-1, -1, 2] ]**

**分析**：

这道题以前接触过，因此还是能想到方法的。朴素解法就是三重循环分别相加看看和是否为 0 ，然而复杂度达到了 **O(n^3)**。因此必须采用新的方法。

算法：首先对数据进行排序。假设有三个指针 first, second, third，分别指向第一个，first 后一个和最后一个排序后的元素。固定 first，计算三个指针指向的数字的和 sum，有以下几种情况：

1. **sum < 0**：偏小，需要寻找更大的，因为first固定，所以我们让 second++。
2. **sum > 0**：偏大，需要寻找更小的，让 third-\-。
3. **sum == 0**：找出一个解，同时让 second++, third-\-。为了去重复，所以如果新的 second 和之前一样则直接跳过，third 亦然。

进行一趟扫描后，增加 first 的值，如果 first 值和之前一样则没有必要重复计算，直接自增即可。


采用上述算法，复杂度可以降到 **O(n^2)**，顺利 AC。

**实现**：

C++ 实现：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans; //#include <vector>
        if (nums.size() < 3) {
            return ans;
        }
        vector<int> ansTemp;
        sort(nums.begin(), nums.end());  //#include <algorithm>
        
        int first = 0;
        while (first < nums.size() - 2) {
            ansTemp.push_back(nums[first]);
            int second = first + 1, third = nums.size() - 1;
            while (second < third) {
                int sum = nums[first] + nums[second] + nums[third];
                if (sum < 0) {
                    ++second;
                } else if (sum > 0) {
                    --third;
                } else {
                    ansTemp.push_back(nums[second]);
                    ansTemp.push_back(nums[third]);
                    ans.push_back(ansTemp);
                    while (second < third && nums[++second] == ansTemp[1]);
                    while (second < third && nums[--third] == ansTemp[2]);
                    ansTemp.pop_back();
                    ansTemp.pop_back();
                }
            }
            ansTemp.pop_back();
            while (first < nums.size() - 2 && nums[first] == nums[++first]);
        }
        return ans;
    }
};
```

小炫耀一下（虽然不知道为啥会这样）：

![](../images/leetcode/P15.png)

---

## #9. Palindrome Number

Difficulty: Easy

**问题描述**：

Determine whether an integer is a palindrome. Do this without extra space.

**分析**：

一开始的想法很简单，int 转换为字符串然后直接递归判断是否回文即可。然后仔细一看题目有一句：**Do this without extra space**. 而转换字符串需要新的的空间开销。然后再的想法就是倒转这个 int，如果倒转后的 reverse_x 和 x 相等，便是回文数。

**实现**：

C 实现：

```c
int isPalindrome(int x) {
    if (x < 0) {
        return 0;
    }
    int reverseTemp = x;
    int reverse = 0;
    while (reverseTemp) {
        reverse = 10 * reverse + reverseTemp % 10;
        reverseTemp /= 10;
    }
    return reverse == x;
}
```

---


## #23. Merge k Sorted Lists

Difficulty: Hard

**问题描述**：

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

**分析**：

k 路归并，朴素解法是每次都比较链表头 k 个元素的大小，然后选择最小的进入 ans 中。复杂度 **O(k N)**.

如果使用分治，将 k 路两两合并，然后一直递归下去，复杂度为 **O(logk N)**.


**实现**：

C++ 实现：

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int v) : val(v), next(NULL) {} 
};

class Solution {
public:
    ListNode* merge2Lists(ListNode* lists1, ListNode* lists2);
    ListNode* mergeKLists(vector<ListNode*>& lists);
};

ListNode* Solution::mergeKLists(vector<ListNode*>& lists) {
    if (lists.size() == 0) {
        return NULL;
    } else if (lists.size() < 2) {
        if (lists[0]) {
            return lists[0];
        }
        return NULL;
    } else if (lists.size() == 2) {
        return merge2Lists(lists[0], lists[1]);
    } else {
        vector<ListNode*> temp;
        int i = 0;
        while (i < lists.size()) {
            if (i != lists.size() - 1) {
                temp.push_back(merge2Lists(lists[i], lists[i + 1]));
                i += 2;
            } else {
                temp.push_back(lists[i]);
                i += 1;
            }
        }
        return mergeKLists(temp);
    }
}

ListNode* Solution::merge2Lists(ListNode* lists1, ListNode* lists2) {
    if (!lists1 || !lists2) {
        if (lists1) {
            return lists1;
        }
        if (lists2) {
            return lists2;
        }
        return NULL;
    }

    ListNode *l1Head = lists1, *l2Head = lists2;
    ListNode *ans = NULL, *temp = NULL;
    if (l1Head->val < l2Head->val) {
        ans = l1Head;
        l1Head = l1Head->next;
        ans->next = NULL;
    } else {
        ans = l2Head;
        l2Head = l2Head->next;
        ans->next = NULL;
    }
    temp = ans;
    while (l1Head && l2Head) {
        if (l1Head->val < l2Head->val) {
            temp->next = l1Head;
            l1Head = l1Head->next;
            temp = temp->next;
            temp->next = NULL;
        } else {
            temp->next = l2Head;
            l2Head = l2Head->next;
            temp = temp->next;
            temp->next = NULL;
        }
    }
    if (l1Head) {
        temp->next = l1Head;
    }
    if (l2Head) {
        temp->next = l2Head;
    }

    return ans;
}
```

---

## #33. Search in Rotated Sorted Array

Difficulty: Medium

**问题描述**：

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

**(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2)**.

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

**分析**：

最简单的方法就是顺序查找，复杂度 **O(n)**，这样的话，给出的 array 的特性完全没有利用到，所以一定存在更高效的算法。

因为这只是一个 sorted array 中间旋转了一下，相当于两个 sorted array，而且左边 array 的最小值 > 右边 array 的最大值。所以我们要做的，就是在找到 target 在哪个 array 中，之后采用二分查找即可。

选出 nums 中间的 **mi-index** 元素，判断这个元素左边和右边是否构成了顺序序列。

只需要对比 nums[mi] 是否 < nums[hi] 或 nums[mi] 是否 > nums[lo] 即可，所以这一步一定能够找到一个 sorted 序列。

找到后判断 target 是否在这个 sorted 序列里面，如果在里面直接 Binary Search 即可解决此问题。如果 target 不在 sorted 序列里，则必然在另一个序列里，根据条件赋值 mi 给 lo 或者 hi，重复上述过程即可解决该问题。

因为每一次都会减小问题规模为原来的一半，复杂度和 Binary Search 一致：**O(log n)**

**实现**：

C 实现：

```c
int search(int* nums, int numsSize, int target) {
    if (numsSize == 1) {
        return nums[0] == target ? 0 : -1;
    }
    int lo = 0, hi = numsSize;
    while (lo < hi) {
        int mi = (lo + hi) >> 1;
        if (nums[mi] == target) {
            return mi;
        }
        if (nums[mi] <= nums[hi - 1]) {
            if (target > nums[mi] && target <= nums[hi - 1]) {
                lo = mi + 1;
            } else {
                hi = mi;
            }
        } else {
            if (target >= nums[lo] && target < nums[mi]) {
                hi = mi;
            } else {
                lo = mi + 1;
            }
        }
    }
    return -1;
}
```

---
