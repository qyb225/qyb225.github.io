---
layout: post
title:  "二元分类及 Logistic 回归"
date:   2017-08-12 00:00
categories: Deep-Learning
permalink: /deep-learning/logistic-regression
---


# 二元分类

已知训练样本是个 n 维向量，即 n 个 feature，输出的结果只有两种，用 1 / 0 表示，这就是二元分类问题。

例如：给定肿瘤细胞大小，生长时间和生长部位 3 个 feature，判断肿瘤是良性或者恶性；又或者是给定64 * 64图片每个像素的 RGB 值，也就是一个 64 * 64 * 3 维的向量作为输入，判断这张图片是不是一只猫的图片。

---

## Logistic Regression

**训练集：**

n维向量x：

![](http://latex.codecogs.com/gif.latex?
$$
x = 
\left[
\begin{matrix}
 x1     \\
 x2    \\
 \vdots \\
 xn     \\
\end{matrix}
\right]
$$)

假设 参数 **&omega;**, 同样为 n 维向量，另外还有参数 **b**，使得：

![](http://latex.codecogs.com/gif.latex?
$$
z = \omega^T· x + b
$$)

但是我们发现问题：z可以是一个很大或者很小的数，而我们要预测的是 0 / 1，因此，我们引入 Logistic 函数

![network](../images/deep-learning/logistics.png)

表达式：

![](http://latex.codecogs.com/gif.latex?
$$
\sigma(z)=\frac{1}{1 + e^{-z}}
$$)

因此设置预测的值 **ŷ** 会严格在0和1之间

---

### Loss Function

![](http://latex.codecogs.com/gif.latex?
L(\hat{y}, y) = -(y\log \hat{y} + (1-y)\log (1 - \hat{y}))
$$)

---

#### 当 y = 1 时：

<div><img src="http://latex.codecogs.com/gif.latex?$$L(\hat{y}, y) = - \log \hat{y}$$" /></div>

我们希望Loss function 小，因为 0 < ŷ < 1 所以 log ŷ < 0, - log ŷ > 0 且 ŷ -> 1，- log ŷ 越接近 0，所以此时 **ŷ 越接近 1 越好**

---

#### 当 y = 0 时：

<div><img src="http://latex.codecogs.com/gif.latex?$$L(\hat{y}, y) = - \log (1 - \hat{y})$$" /></div>

同样希望Loss function 小，1 - ŷ 越大越好，所以此时 **ŷ 越接近 0 越好**

---

### Cost Function

Cost Function 为 m 个样例中所有 Loss Function 的算术平均：

![](http://latex.codecogs.com/gif.latex?
$$
J(\omega, b) \\\\= \frac {1}{m}\sum_{i=1}^{m}L(\hat{y}^{(i)}, y^{(i)})
\\\\= -\frac {1}{m}\sum_{i=1}^{m}(y^{(i)}\log \hat{y}^{(i)} + (1-y^{(i)})\log (1 - \hat{y}^{(i)}))
$$)