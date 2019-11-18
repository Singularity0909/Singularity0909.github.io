---
title: 题解 洛谷P4980 【模板】Polya定理
date: 2019-08-28 23:24:58
categories: DS&A
tags:
- 数论
- 组合数学
- Polya定理
- 欧拉函数
thumbnail: https://cdn.jsdelivr.net/gh/singularity0909/cdn/img/gallery/icpc.jpg
mathjax: true
---

## 题目描述

给定一个n个点，n条边的环，有n种颜色，给每个顶点染色，问有多少种本质不同的染色方案，答案对10^9+7取模

注意本题的本质不同，定义为：只需要不能通过旋转与别的染色方案相同。

## 输入格式

第一行输入一个t，表示有t组数据

第二行开始，一共t行，每行一个整数n，意思如题所示。

## 输出格式

共t行，每行一个数字，表示染色方案数对10^9+7取模后的结果

## 输入输出样例

**输入 #1**

```
5
1 
2 
3 
4 
5 
```

**输出 #1**

```
1
3
11
70
629
```

## 说明/提示

n≤10^9

t≤10^3

## 题解

根据Polya定理易知答案为 $\frac{1}{n}\sum_{i=1}^{n}n^{gcd\left(i,n\right)}$ 。

考虑优化：设g(i)为gcd(i, n)，显然所有的g都是n的因数，故可枚举n的因数。

当枚举到n的第i个因数时，总存在 $g=\frac{n}{p_{i}}$ 。

设有x个g值相同，不难发现 $x=\varphi\left(\frac{n}{g}\right)=\varphi\left(p\right)$ 。

故答案可化简： $\frac{1}{n}\sum_{i=1}^{n}n^{gcd\left(i,n\right)}=\frac{1}{n}\sum_{p|n}\varphi \left(p\right)\cdot n^{\frac{n}{p}}=\sum_{p|n}\varphi \left(p\right)\cdot n^{\frac{n}{p}-1}$ 。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int mod = 1e9 + 7;

inline const int read()
{
    int x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}

ll quick_pow(ll x, ll n)
{
    ll res = 1;
    while (n)
    {
        if (n & 1) res = res * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return res;
}

ll euler(int n)
{
    ll res = 1;
    for (int i = 2; i <= sqrt(n); i++)
    {
        if (n % i == 0)
        {
            n /= i; res *= i - 1;
            while (n % i == 0) { n /= i; res *= i; }
        }
    }
    if (n > 1) res *= n - 1;
    return res;
}

ll polya(int n)
{
    ll res = 0;
    for (int i = 1; i <= sqrt(n); i++)
        if (n % i == 0)
            res = i * i == n ? (res + euler(i) * quick_pow(n, n / i - 1)) % mod : (res + euler(i) * quick_pow(n, n / i - 1) + euler(n / i) * quick_pow(n, i - 1)) % mod;
    return res;
}

int main()
{
    int t = read();
    while (t--)
    {
        int n = read();
        printf("%lld\n", polya(n));
    }
    return 0;
}
```