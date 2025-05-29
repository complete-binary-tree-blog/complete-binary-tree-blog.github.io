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

本文将介绍两种算法：FFT，FNTT。

<!-- more -->

# FFT

**快速傅里叶变换**（Fast Fourier Transform）是基于**傅里叶变换**（Fourier Transform），并使用**复数**优化的算法，可用于处理多项式乘法。

## 前置知识：复数

**虚数单位**用 $\mathrm i$（**不能**使用 $i$）表示，满足 $\mathrm i^2=-1$。

???+ failure "错误理解"

    $\sqrt {-1}=\mathrm i$ 是不严谨的。

**复数**是一种有两个部分的数，包含实部和虚部，通常表示为 $z=x+y\mathrm i$，其中 $\mathrm i$ 是虚数单位。

**复数的模**定义为 $\sqrt{x^2+y^2}$，通常写作 $|z|$。

**单位根**若复数 $\omega$ 满足 $\omega^n=1$，则称 $\omega$ 是 $n$ 次单位根，可以用 $\omega_n$ 表示。

单位根还可以这样算出：$\omega_n=\mathrm e^{\mathrm i \cdot \frac{2k\pi}{n}}$，其中 $k \in [0,n)$ 且 $k \in \mathbb Z$。

当然，单位根还有一个性质：若 $n$ 为偶数，则 $\omega^{\frac n 2}=-1$。

**复数加法**若 $z_1=a_1+b_1\mathrm i$，$z_2=a_2+b_2\mathrm i$，则 $z_1+z_2=(a_1+a_2)+(b_1+b_2)\mathrm i$。

**复数减法**若 $z_1=a_1+b_1\mathrm i$，$z_2=a_2+b_2\mathrm i$，则 $z_1-z_2=(a_1-a_2)+(b_1-b_2)\mathrm i$。

**复数乘法**若 $z_1=a_1+b_1\mathrm i$，$z_2=a_2+b_2\mathrm i$，则 $z_1 \times z_2=(a_1a_2-b_1b_2)+(a_1b_2+b_1a_2)\mathrm i$。

可以证明，复数加减法、复数乘法分别满足交换律、结合律，复数乘法与加减法满足分配律。

## 多项式点值表示法

我们知道，已知一些点对 $(x_i,y_i)$，其中 $\forall i,j \in [1,n],x_i \not= x_j$，那么我们可以**唯一**确定一个 $n-1$ 次多项式。

???+ question "这不是插值吗？"

    还真是。

    因为 $n$ 个点可以确定一个 $n-1$ 次多项式。

假设这两个要乘起来的多项式分别是 $f(x)$、$g(x)$，那么我们可以取一些值 $x_i$，则 $(x_i,f(x_i) \cdot g(x_i))$ 这个点肯定在他们俩乘起来的多项式上。

然而，我们要想办法如何快速实现求值和确定多项式呢？

## 借助单位根

我们取 $\omega=\mathrm{e}^{\mathrm{i} \cdot \frac{2\pi}{n}}$，取这个值的目的是为了 $\omega^n=1$，即 $\omega$ 是 $n$ 次单位根。

???+ question 为什么不取其他使 $\omega^n=1$ 的值？

    也可以，不过这样取可以更方便求单位根。
    
    而且有一些单位根可能会存在整数 $k<n$，使得 $\omega_n^k=1$，这样子 $\{\omega_n^0,\omega_n^1,\omega_n^2,\dots,\omega_n^n\}$ 中会**有重复**，就不满足点值表示的要求（点值表示中 $x$ **不可重复**）。

我们很容易求出 $n$ 对点对 $(\omega^i,f(\omega^i))$。

那么怎么知道点对求多项式呢？

设有点对 $\{(\omega^i,y_i)\}$，多项式为 $f(x)=\sum_{i=0}^{n-1}a_ix^i$。

直接求太困难了，~~伟大的逆傅里叶变换告诉我们~~我们可以构造一个序列 $c$ 满足

$$c_i = \sum_{j=0}^{n-1}y_j\omega^{-ij}$$

也就是分别把点 $[\omega^{-0i},\omega^{-i},\omega^{-2i},\dots,\omega^{-(n-1)i}]$ 代入求多项式 $\sum_{i=0}^ny_ix^i$ 的值。

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

这个式子和刚刚求多项式的式子差不多（只不过 $\omega^{ij}$ 变成了 $\omega^{-ij}$），所以我们可以利用类似求多项式值的方法求出。

## 分治优化

不过到目前算法复杂度还是只能达到 $O(n^2)$，我们考虑分治优化。

现在我们有了两个式子：

$$y_i=\sum_{j=0}^{n-1}a_j\omega^{ij}$$

$$a_i = \frac1n\sum_{j=0}^{n-1}y_j\omega^{-ij}$$

然而朴素地计算它们时间复杂度是 $O(n^2)$ 的，无法接受。

所以我们考虑奇偶分治。（先把 $\omega^i$ 替换成 $x$ 不然看的难受）

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

$$y_i=f_1(\omega^{2i})+\omega^{i}f_2(\omega^{2i})$$

代入 $\omega^{i+\frac n 2}$

$$\begin{align*}
y_{i+\frac n 2} &= f_1(\omega^{2(i+\frac n 2)})+\omega^{i + \frac n 2}f_2(\omega^{2(i+\frac n 2)})\\
    &= f_1(\omega^{2i})-\omega^{i}f_2(\omega^{2i})
\end{align*}$$

可以发现这两个式子（几乎）完全一样。

所以由于单位根性质，只要算 $y_0,y_1,\dots,y_{\frac n 2-1}$ 就能 $O(1)$ 得出 $y_{\frac n 2},y_{\frac n 2+1},\dots,y_{n-1}$。

所以可以进行分治。

时间复杂度 $T(n)=O(n)+2T(\frac n 2)$，即 $O(n \log n)$。

## 核心代码

```cpp
typedef complex<double> comp;
const double pi=acos(-1.0); // cos(pi)=-1

/// @brief 聚 DFT、IDFT 于一体的 FFT 函数。
/// @param n 需要求的多项式长度。
/// @param a 需要求的多项式数组。结果在此处返回。
/// @param type 1 表示 DFT，-1 表示 IDFT
void FastFastTLE(int n, comp*a, int type){
    if(n==1)return; // 常数项直接返回

    comp*a1=new comp[n>>1],*a2=new comp[n>>1];
    for(int i=0;i<n;++i) // 奇偶分类
        if(i&1)a2[i/2]=a[i];
        else a1[i/2]=a[i]; 
    
    // 递归分治
    FastFastTLE(n>>1, a1, type);
    FastFastTLE(n>>1, a2, type);

    // wmul 就是单位根，w 表示 wmul^i
    comp wmul=comp(cos(2.0*pi/n),sin(2.0*pi/n)*type), //这里也可以使用 exp 函数，效果一样
         w=comp(1.0,0.0);
    for(int i=0;i<(n>>1);++i){
        comp tmp=w*a2[i]; // 复数计算比较慢，提前计算节省时间
        a[i]=a1[i]+tmp,a[i+(n>>1)]=a1[i]-tmp; // 式子
        w*=wmul;
    }

    delete[] a1;
    delete[] a2;
}
```

# FNTT

**快速数论变换**（Fast Number-Theoretic Transform）是一种基于**数论变换**（Number-Theoretic Transform），并利用**原根**进行优化的算法，可以处理带模数的多项式乘法。

在不引起混淆的语境下，FNTT 也可以简称为 NTT。

## 前置知识：原根

在模 $p$ 意义下，**原根** $g$ 是一个满足以下条件的整数：

1. $g^{\varphi(p)} \equiv 1 \pmod p$；

2. $\forall i \in [1,\varphi(p)),g^{i}\not\equiv 1\pmod p$；

3. $g \in [0,p)$。

在 $p$ 为奇质数时，$\varphi (p)=p-1$，此时它具有以下性质：

如果我们设 $n$ 为偶数，$\omega=g^{\frac {p-1}n}$，那么有：

1. $\omega^{n}=g^{p-1}=1$；

2. $\omega^{0},\omega^1,\omega^2,\dots,\omega^{n-1}$ 两两不同（原根第 $2$ 条）；

3. $\omega^{\frac n 2} \equiv -1 \pmod p$。

???+ info "$3$ 的证明"

    由于 $g^{p-1}\equiv 1\pmod p$，即 $g^{2 \cdot \frac{p-1}2}-1 \equiv 0\pmod p$。

    因式分解，$(g^{\frac{p-1}2}+1)(g^{\frac{p-1}2}-1) \equiv 0\pmod p$。

    由于 $g^{\frac{p-1}2} \not \equiv 1$，所以 $g^{\frac{p-1}2} \equiv -1$。

    所以 $\omega^{\frac{n}2}\equiv -1\pmod p$。

根据上面三个性质，我们发现：在模 $p$ 意义下，**原根可以代替 FFT 中的单位根**。

于是我们可以直接套上 FFT，并把单位根替换成原根，然后再加上模数即可。

!!! Warning "注意"

    NTT 对于模数**有要求**。

    对于一个模数 $p$，它可以处理的最长序列是 $2^k$，其中 $k$ 为满足 $2^k \mid p$ 的最大整数。

    如果 $2^k$ 太大了会导致 $\frac{p-1}{2^k}$ 不是整数，就做不了 NTT 了。

    像常见模数 $998344353=7 \times 17 \times 2^{23}$，所以模 $998244353$ 最大可以做 $n=2^{23}$ 的序列。

    对于任意模数 NTT，请见 [任意模数多项式乘法](https://www.luogu.com.cn/problem/P4245)。

## 核心代码

```cpp
typedef long long ll

/// @brief NTT 函数。
/// @param n 需要求的多项式长度。
/// @param a 需要求的多项式数组。结果在此处返回。
/// @param type 1 表示 DFT，-1 表示 IDFT
void Nearly_Totally_TLE(int n,ll* a,int type){
    if(n==1)return;
    ll*a1=new ll[n>>1],*a2=new ll[n>>1];
    for(int i=0;i<n;++i)if(i&1)a2[i/2]=a[i];else a1[i/2]=a[i];
    Nearly_Totally_TLE(n>>1,a1,type),Nearly_Totally_TLE(n>>1,a2,type);
    ll wmul=(type+1)?W[n]:invW[n],w=1;
    for(int i=0;i<(n>>1);++i)a[i]=(a1[i]+w*a2[i])%mod,a[i+(n>>1)]=(a1[i]-w*a2[i])%mod,w=w*wmul%mod;
    delete[] a1;
    delete[] a2;
}
```
