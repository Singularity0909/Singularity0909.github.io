---
title: 题解 UVALive7098 Farey Sums
date: 2019-08-14 01:02:27
categories: DS&A
tags:
- 数论
- 欧拉函数
mathjax: true
---

{% pdf https://vj.ti12z.cn/44b741bc6f1ad13fb43fb1165343f874?v=1565677147 %}

不难发现，序列的增量与欧拉函数有关。

根据欧拉函数

${\displaystyle \varphi (n)=\prod _{i=1}^{r}p_{i}^{k_{i}-1}(p_{i}-1)=\prod _{p\mid n}p^{\alpha _{p}-1}(p-1)=n\prod _{p|n}\left(1-{\frac {1}{p}}\right)}$

可打表再求前缀和。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e4 + 10;
int t, n, sum, phi[maxn], presum[maxn];

inline const int read()
{
    int x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}

void get_euler()
{
    for (int i = 1; i < maxn; i++)
        phi[i] = i;
    for (int i = 2; i < maxn; i++)
        if (phi[i] == i)
            for (int j = i; j < maxn; j += i)
                phi[j] = phi[j] / i * (i - 1);
}

int main()
{
    get_euler();
    for (int i = 1; i < maxn; i++)
        presum[i] = sum += phi[i];
    t = read();
    for (int i = 1; i <= t; i++)
    {
        n = read(); n = read();
        printf("%d %d/2\n", i, presum[n] * 3 - 1);
    }
    return 0;
}

```