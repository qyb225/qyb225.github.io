---
layout: post
title:  "Logistic Regression Gradient Descent"
date:   2017-08-12 09:00
categories: Deep-Learning
permalink: /deep-learning/gradient-decsent
---

# Gradient Descent ( 梯度下降 )

在之前的Logistic Regression 中，我们得到了整体的 Cost Function：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
J(\omega, b) \\\\= \frac {1}{m}\sum_{i=1}^{m}L(y_p^{(i)}, y^{(i)})
\\= -\frac {1}{m}\sum_{i=1}^{m}(y^{(i)}\log y_p^{(i)} + (1-y^{(i)})\log (1 - y_p^{(i)}))
$$" /></div><br />

而我们要做的，就是最小化这个Cost Function。

---

## 算法原理

最简单的一个参数的例子说起

![](../images/deep-learning/gradient-descent.jpg)

我们可以利用如下的公式：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\omega := \omega - \alpha \frac {d J(\omega)}{d \omega}
$$" /></div>

* **当 &omega; < 局部最优解的时候，导数 < 0，迭代让 &omega; 增大**
* **当 &omega; > 局部最优解的时候，导数 > 0，迭代让 &omega; 减小**

<br />

推广到多参数类型，&omega; 为 n 维向量，b 为 实数，则：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\omega := \omega - \alpha \frac {\partial J(\omega, b)}{\partial \omega}
$$" /></div><br />

<div><img src="http://latex.codecogs.com/gif.latex?
$$
b := b-\alpha \frac {\partial J(\omega, b)}{\partial b}
$$" /></div><br />

---

## Logistic Regression 迭代公式

刚好闲来无事，决定自己推一发这个公式，就当复习一下高数。

### 推导：

其中最重要的便是求出：

<div><img src="http://latex.codecogs.com/gif.latex?
$$\frac {\partial J(\omega, b)}{\partial \omega}$$" /></div><br />

设x(i)为2维向量：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\hat{y} = \sigma(z)=\frac{1}{1 + e^{-z}}
$$" /></div><br />

<div><img src="http://latex.codecogs.com/gif.latex?
$$
z^{(i)} = \omega_1 x_1^{(i)} + \omega_2 x_2^{(i)} + b
$$" /></div><br />

<div><img src="http://latex.codecogs.com/gif.latex?
$$
J(\omega, b) = -\frac {1}{m}\sum_{i=1}^{m}(y^{(i)}\log \hat{y}^{(i)} + (1-y^{(i)})\log (1 - \hat{y}^{(i)}))
$$" /></div><br />


微积分常识得到：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial J(\omega, b)}{\partial \omega}\\\\
= \frac {\partial J(\omega, b)}{\partial \hat{y}} 
* \frac {\partial \hat{y}}{\partial z} 
* \frac {\partial z}{\partial \omega} 
$$" /></div>

**依靠这个式子，下面开始逐步化简**。

---

#### 第一项：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial J(\omega, b)}{\partial \hat{y}} \\\\
= -\frac {1}{m}\sum_{i=1}^{m}(\frac {y}{\hat{y}} - \frac{1 - y}{1 - \hat{y}})
$$" /></div><br />

为了方便起见，先省略前面的求和符号,结尾处补上。

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {y}{\hat{y}} - \frac{1 - y}{1 - \hat{y}} \\\\
= y + ye^{-z} + (y - 1)(e^z + 1) \\\\
= y(2 + e^z + e^{-z}) - e^z - 1
$$" /></div>

---

#### 第二项：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial \hat{y}}{\partial z} 
= \frac {1}{e^z + e ^{-z} + 2}
$$" /></div><br />

---

#### 第一项 × 第二项：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {y(2 + e^z + e^{-z}) - e^z - 1}{e^z + e ^{-z} + 2} \\\\
= y - \frac{e^z + 1}{e^z + e ^{-z} + 2} \\\\
= y - \frac{e^{-z}(e^z + 1)}{(e^{-z} + 1)^2} \\\\
= y - \frac{1 + e^{-z}}{(e^{-z} + 1)^2} \\\\
= y - \frac{1}{e^{-z} + 1}
$$" /></div>

---

#### 第三项：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial z}{\partial \omega} 
= x
$$" /></div>

----

#### 补上求和符号，整体化简：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial J(\omega, b)}{\partial \omega}
= -\frac {1}{m}\sum_{i=1}^{m}(y - \frac{1}{e^{-z} + 1})x
$$" /></div><br />

**我们注意到，还可以化简为**：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\frac {\partial J(\omega, b)}{\partial \omega}\\\\
= -\frac {1}{m}\sum_{i=1}^{m}(y - \hat{y})x \\\\
= \frac {1}{m}\sum_{i=1}^{m}(\hat{y} - y)x
$$" /></div><br />


---

### 结论：

得到 Logistic Regression 的 Gradient descent公式：

<div><img src="http://latex.codecogs.com/gif.latex?
$$
\omega := \omega - \frac {\alpha}{m}\sum_{i=1}^{m}(\hat{y} - y)x
$$" /></div><br />
