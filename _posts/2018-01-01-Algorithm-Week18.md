---
layout: post
title:  "Algorithm Week 18"
date:   2018-01-01 20:30
categories: Algorithm
permalink: /algorithm/week18
---

# 算法 Ex 8.3 证明

STINGY SAT is the following problem: given a set of clauses (each a disjunction of literals) and an integer k, find a satisfying assignment in which at most k variables are true , if such an assignment exists. Prove that STINGY SAT is NP-complete.

**证明**：

可以通过把 STINGY SAT 问题归约成 SAT 问题来进行求解： 
假设现在有一个 SAT 问题的实例 **I**，令 **k=|I|** 表示实例 I中变量的个数，则 **(I,k)** 就是 STINGY SAT 问题的一个实例。

**只需证明， I 有解时，(I, k) 也有解； I 无解，(I, k) 也无解**。

分两种情况：

* 假设 I 有一个解 S，令 s 表示 S 中的 true 元素，则 s <= k，此时，S 显然是实例 (I,k) 的一个解。

* 假设 I 无解，可以用反正证明 (I, k) 无解。

由此我们证明了：STINGY SAT 问题可以归约成 SAT 问题，因此它是个 NP 完全问题。