---
title: 一些笔记
permalink: notes/
date: 2023-05-01 21:12:21
---
# 二元一次不定方程

$ax + by = c$
$a, b, c$ 为给定整数
求整数解 $x, y$

$\red\star$ 当 $\gcd(a,b) \mid c$ 时有解

例子：$3x + 4y = 1$

易得特解：$x = -1,\ y = 1$
其它解：

- 每次使 $x$ 加 $4$，$y$ 减 $3$

$$
x = 5,\ y = -2 \\
x = 9,\ y = -5 \\
x = 13,\ y = -8 \\
\dots \dots
$$

- 或 $x$ 减 $4$，$y$ 加 $3$

$$
x = -5,\ y = 4 \\
x = -9,\ y = 7 \\
\dots \dots
$$

易得通解：

$$
x = -1 + 4t,\ y = 1 - 3t \\
t = 0,\ \pm 1,\ \pm 2,\ \dots
$$

对于方程 $ax + by = c$，若找到一组特解 $x_0,\ y_0$，则通解的形式为：

$$
x = x_0 + \frac{b}{\gcd(a, b)}t,\ y = y_0 - \frac{a}{\gcd(a, b)}t
$$

## 扩展欧几里得算法
$\dots$

# 矩阵乘法

$(A \times B)_{ij} = \sum_k A_{ik}B_{kj}$
$A$ 的大小为 $n \times k$
$B$ 的大小为 $k \times m$
$(A * B)$ 的大小为 $n \times m$

$\red\star$ 矩阵乘法不满足交换律