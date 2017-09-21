---
layout: post
title:  "DES 加密算法"
date:   2017-09-21 20:30
categories: Information-Security
permalink: /information-security/des
---

# 对称加密算法：Data Encryption Standard

（PS，还没写完）

Data Encryption Standard （数据加密标准），简称 DES，是一种经典的对称加密算法。设计遵循 “香农xx”：xxxxxxxxx。

DES 通过一个 64 位的密钥（除去校验位后有效位为 56），并且把信息分割成 64 位的分组并逐一加密。它具有以下特点：

* 密钥会影响密文输出
* 明文同样会影响密文输出

作为一个经典的加密算法，DES 在数十年经久不衰，本文则希望通过自顶向下的方法，探讨一下 DES 算法的具体原理。

---

## DES 流程概览

在 DES 算法中，明文会被分组成 64 位一组。而每一组依次进入算法加密，因此在此我们只讨论 64位一组的加密。

---

###  Step 1: IP 置换

明文为 64bits 的信息，经过一个初始置换函数 IP，重新排列后输出。并分割成左右两部分 **L0** (32bits)，**R0** (32bits)。


### Step 2: Feistel 迭代

IP 置换后的 L0, L1 以及 64bits 的（子）密钥 k 作为 Feistel 函数的输入，经过 16 次迭代，输出 L16 (32bits), R16 (32bits)。


### Step 3: IP 逆置换

将经过 Feistel 迭代的左右子串 L16，R16 按照 **{ R16, L16 }** 顺序排列，经过 IP 置换的逆关系 IP^(-1)，产生最后输出的密文。

---

忽略一部分细节，整个流程可用下图表示：

![des-overall](../images/information-security/des-overall.png)

---

## Feistel 轮函数详解

### 16次迭代过程

在 IP 置换后，经过重新排列混淆的 64bits 信息被分为左右两部分： L0(32bits), R0(32bits)，这时候，它们要经过 16 次迭代，具体迭代过程如下图：

![](../images/information-security/T-iteration.png)

* **L[i]**： 直接为上一层的 R[i - 1]

* **R[i]**： 略微复杂一些，它是上一层的 L[i - 1] 与 Feistel 函数返回值的异或。

* **Feistel 函数**： 接受两个参数：R[i - 1] 和 k[i] （后面详解），返回一个 32bits 的值。

---

### Feistel 函数

* 参数：R[i - 1]: 32bits, k[i]: 48bits

* 返回：32bits

---

#### **总流程**

1. 32bits 的 R[i - 1] 通过 **E 扩展**，变为 48bits 的 E(R[i - 1])。
2. E(R[i - 1]) **xor** k[i]，结果为 48bits。
3. 将上述 xor 操作得到的结果分为 8 份，每份就是 6bits。
4. 让第 i 份通过 **S(i)Box**，将会从 6bits -> 4bits。
5. 将 8 份 4bits 数据组合成 32bits 的数据。
6. 将上一步得到的 32bits 数据经过 P置换，然后输出 32bits 数据。

如下图：

![](../images/information-security/feistel.png)

---

#### **E 扩展**

Feistel 函数的一个步骤是：将 32bits 的 R[i - 1] 扩展成 48bits 的 E(R[i - 1])，这个过程不难理解。

首先，将 32bits 的 R[i - 1] 转化为 **8 * 4** 的矩阵。

$$
\left[
\begin{matrix}
 1 & 2 & 3 &  4   \\
 5 & 6 & 7 & 8   \\
 \vdots & \vdots & \vdots& \vdots \\
 29 & 30 & 31 & 32     \\
\end{matrix}
\right]
$$

现在，我们要扩展这个矩阵，使其扩展成 **8 * 6** 的矩阵，**在首尾各增加一列**：

$$
\left[
\begin{matrix}
 32 & 1 & 2 & 3 & 4 & 5   \\
 4 & 5 & 6 & 7 & 8 & 9   \\
 \vdots & \vdots &\vdots & \vdots & \vdots& \vdots \\
 28 & 29 & 30 & 31 & 32 & 1    \\
\end{matrix}
\right]
$$

利用原矩阵数据，依次循环向后扩展。

---

## k[i] 生成

在上述 Feistle 函数中，另一个参数就是 48bits 的 k[i]，而 k[i] 便是通过密钥 k 生成的。