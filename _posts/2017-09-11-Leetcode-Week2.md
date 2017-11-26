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

## #53. Maximum Subarray

Difficulty: Easy

**问题描述**：

Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the **array [-2,1,-3,4,-1,2,1,-5,4]**,

the contiguous subarray **[4,-1,2,1]** has the largest **sum = 6**.

**分析**：

这道题其实不是个 Easy 的题目。感慨是确实没想到除了 **O(n^2)** 的算法，查阅资料后发现，需要使用著名的 **Kadane's Algorithm**。

首先，题目需要两个变量记录和，一个叫做 **sum**，更像是一个中间变量，另一个是 **max**，用于记录已知出现的最大的子序列的和。

明确这一点：最大子序列**首和尾必定不可能是和为负数的子序列**，因为去掉首尾依然是子序列，且和一定比加上负数首尾的子序列的和要大。

算法主体：

* 一次遍历，sum 负责从 0 开始加每一个数字，一旦 sum < 0，因为上述原因，这个子序列必定会被淘汰，所以需要重置 sum 为 0。

* max 从 0 开始不断和 sum 比较，一旦 sum > max，更新 max 的值。

**实现**：

C 实现：

```c
int maxSubArray(int* nums, int numsSize) {
    if (numsSize < 1) {
        return 0;
    }
    int i, sum = 0, max = nums[0];
    for (i = 0; i < numsSize; ++i) {
        sum += nums[i];
        if (sum > max) {
            max = sum;
        }
        if (sum < 0) {
            sum = 0;
        }
    }
    return max;
}
```

---

## #169. Majority Element

Difficulty: Easy

**问题描述**：

Given an array of size n, find the majority element. The majority element is the element that appears more than **n / 2** times.

You may assume that the array is non-empty and the majority element always exist in the array.

**分析**：

如果 majority number 出现了 n / 2 次以上，那么其它的 number 的数量总和也不会有它多。

随机选择一个 number 作为 majority，一个计数器 count。遍历整个 array，如果 array[i] == majority，那么我们让 count + 1。

如果不相等则检查 count 是否为 0， 如果为 0 则重新选择当前的 array[i] 为新的 majority。

如果 count 仍然 > 0，自减 count 即可。

**实现**：

C 实现：

```c
int majorityElement(int* nums, int numsSize) {
    int ans = nums[0];
    int i, count = 0;
    for (i = 0; i < numsSize; ++i) {
        if (ans == nums[i]) {
            ++count;
        } else if (count == 0) {
            ans = nums[i];
        } else {
            --count;
        }
    }
    return ans;
}
```
---

## #55. Jump Game

Difficulty: Medium

**问题描述**：

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**A = [2,3,1,1,4]**, return **true**.

**A = [3,2,1,0,4]**, return **false**.

**分析**：

我们先假设如果不能达到会发生什么，也就是其中有一个位置，让前面的位置跨越最大的 step 都达不到。于是我想到一个思路，逆序遍历整个序列。

一个点和自己的距离为 0，设置一个 count，每次向前走一步，count++。如果向前走的那一步的点可以跳到这个位置，那么count 置为 0，意思是从当前点开始，继续寻找前方的点。 整个算法只需要扫描一趟，复杂度 **O(n)**

**实现**：

C 实现：

```c
int canJump(int* nums, int numsSize) {
    if (numsSize < 2) {
        return 1;
    }
    int i, count = 0;
    for (i = numsSize - 1; i >= 0; --i) {
        if (nums[i] >= count) {
            count = 0;
        }
        ++count;
    }
    return count == 1;
}
```