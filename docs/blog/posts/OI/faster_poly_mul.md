---
date:
    created: 2025-05-28
#draft: true # 草稿
pin: false # 置顶
readtime: 15 # 阅读时间
categories: 
    - OI # 板块
links: # 相关链接
    []
tags: # 标签
    - 数学
    - 多项式
    - 分治
    - 乘法
    - O(nlogn) 算法
    - 复数
authors: # 作者
    - cbt
---

# 多项式乘法优化

如何借助复数和分治，将多项式乘法从 $O(n^2)$ 优化到 $O(n \log n)$？

<!-- more -->

## 点值表示法

我们知道，已知一些点对 $(x_i,y_i)$，其中 $\forall i,j \in [1,n],x_i \not= x_j$，那么我们可以唯一确定一个 $n-1$ 次多项式。

???+ question "这不是插值吗？"

    还真是。

    因为 $n$ 个点可以确定一个 $n-1$ 次多项式。

假设这两个要乘起来的多项式分别是 $f(x)$、$g(x)$，那么我们可以取一些值 $x_i$，那么 $f(x_i) \cdot g(x_i)$ 肯定在他们俩乘起来的多项式上。

然而，我们要想办法如何快速实现求值和确定多项式呢？

## 单位根

我们取 $\omega=\mathrm{e}^{\mathrm{i} \cdot \frac{2\pi}{n}}$，取这个值的目的是为了 $\omega^n=1$，即 $\omega$ 是 $n$ 次单位根。

???+ question 为什么不取其他使 $\omega^n=1$ 的值？

    也可以，不过这样取可以更方便求单位根。

我们可以求出 $n$ 对点对 $(\omega^i,f(\omega^i))$。

那么怎么知道点对求多项式呢？

设有点对 $\{(\omega^i,y_i)\}$，多项式为 $f(x)=\sum_{i=0}^{n-1}a_ix^i$。

直接求太困难了，我们构造一个序列 $c$ 满足

$$c_i = \sum_{j=0}^{n-1}y_j\omega^{-ij}$$

也就是说分别把点 $[\omega^{-0i},\omega^{-i},\omega^{-2i},\dots,\omega^{-(n-1)i}]$ 代入求多项式 $\sum_{i=0}^ny_ix^i$ 的值。

考虑把 $y_j=\sum_{k=0}^{n-1}a_k\omega^{jk}$ 代入式子：

$$\begin{align*}
c_i &= \sum_{j=0}^{n-1}y_j\omega^{-ij}\\
    &= \sum_{j=0}^{n-1}\left(\sum_{k=0}^{n-1}a_k\omega^{jk}\right)\omega^{-ij}\\
    &= \sum_{k=0}^{n-1}\sum_{j=0}^{n-1}a_k\omega^{jk-ij}\\
    &= \sum_{k=0}^{n-1}a_k\sum_{j=0}^{n-1}\omega^{j(k-i)}
\end{align*}$$

我们考虑 $\sum_{k=0}^{n-1}a_k\sum_{j=0}^{n-1}\omega^{j(k-i)}$：

1. 如果 $k=i$

    那么后面的 $\omega^{j(k-i)}$ 项全都变成了 $\omega^{0j}$，也就是 $1$。

    所以 $c_i=a_i\sum_{j=0}^{n-1}1=na_i$。

2. 如果 $k \not= i$

    那么 $\sum_{j=0}^{n-1}\omega^{j(k-i)}$ 相当于一个公比为 $\omega^j$ 的等比数列。

    套求和公式，$\sum_{j=0}^{n-1}\omega^{j(k-i)}=1^{k-i} \cdot \frac{1-\omega^{n(k-i)}}{1-\omega^{k-i}}$。

    又 $\omega^n=1$，即 $1^{k-i} \cdot \frac{1-\omega^{n(k-i)}}{1-\omega^{k-i}}=1^{k-i} \cdot \frac{1-1}{1-\omega^{k-i}}=0$。

    所以 $a_k\sum_{j=0}^{n-1}\omega^{j(k-i)}=0$。

综上，$a_i=\frac {c_i} n$，即 

$$a_i = \frac1n\sum_{j=0}^{n-1}y_j\omega^{-ij}$$

## 分治

现在我们有了两个式子：

$$y_i=\sum_{j=0}^{n-1}a_j\omega^{ij}$$

$$a_i = \frac1n\sum_{j=0}^{n-1}y_j\omega^{-ij}$$

然而朴素地计算它们时间复杂度是 $O(n^2)$ 的，无法接受。

所以我们考虑奇偶分治。

$$\begin{align*}
f(x)&= (a_0+a_2x^2+a_4x^4+\dots)+(a_1x+a_3x^3+a_5x^5+\dots)\\
    &= (a_0+a_2x^2+a_4x^4+\dots)+x(a_1+a_3x^2+a_5x^4+\dots)
\end{align*}$$

设 $f_1(x)=a_0+a_2x^1+a_4x^2+\dots$，$f_2(x)=a_1+a_3x^1+a_5x^2+\dots$。

那么

$$f(x)=f_1(x^2)+xf_2(x^2)$$

由于进行了平方，所以进去之后只要算一半（单位根性质，$\{\omega_{n}^{2i}(i \in [1,n])\}=\{\omega_{\frac n2}^{i}(i \in [1,\frac n2])\}$，其中 $\omega_n=\mathrm{e}^{\mathrm{i}\frac{2\pi}{n}}$）。

单位根还有另外一个性质：

上式代入 $\omega^i$

$$y_i=f_1(\omega^{2i})+\omega^{2i}f_2(\omega^{2i})$$

代入 $\omega^{i+\frac n 2}$

$$\begin{align*}
y_{i+\frac n 2} &= f_1(\omega^{2(i+\frac n 2)})+\omega^{2i + \frac n 2}f_2(\omega^{2(i+\frac n 2)})\\
    &= f_1(\omega^{2i})-\omega^{2i}f_2(\omega^{2i})
\end{align*}$$

可以发现这两个式子（几乎）完全一样。

所以由于单位根性质，上式算了左半边可以算右半边。

所以可以进行分治，时间复杂度 $O(n \log n)$。
