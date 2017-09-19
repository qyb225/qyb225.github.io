---
layout: post
title:  "LeetCode Week3"
date:   2017-09-19 20:30
categories: LeetCode
permalink: /leetcode/week3
---

# Problem Set #3

记录第二周 (2017/09/18 - 2017/09/24) 完成的题目。

---

## #56. Merge Intervals

Difficulty: Medium

**问题描述**：

Given a collection of intervals, merge all overlapping intervals.

Given **[1,3],[2,6],[8,10],[15,18]**

return **[1,6],[8,10],[15,18]**

**分析**：

这个题目是求给定区间序列的并区间，脑子里闪过的第一个念头就是按照 所有空间的 start 排序。之后创建 ans 集合并遍历整个 intervals, 可以分为 3 种情况：

* 两个区间没有交集

* 两个空间有交集但不包含

* 前一个空间包含后一个空间

按照不同条件的处理 ans 的末尾元素即可。

**实现**：

C++ 实现：

```cpp
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    vector<Interval> merge(vector<Interval>& intervals);
};
vector<Interval> Solution::merge(vector<Interval>& intervals) {
    vector<Interval> ans;
    if (intervals.size() < 1) {
        return ans;
    }
    sort(intervals.begin(), intervals.end(), [](Interval a, Interval b) {
        return a.start < b.start;
    });
    ans.push_back(intervals[0]);
    int i = 1;
    while (i < intervals.size()) {
        if (intervals[i].start > ans.back().end) {
            ans.push_back(intervals[i]);
        } else if (ans.back().end < intervals[i].end) {
            ans.back().end = intervals[i].end;
        }
        ++i;
    }
    return ans;
}
```

