---
title: 题解 Gym101873B Buildings
date: 2019-08-26 14:03:25
categories: DS&A
tags:
- 组合数学
- Polya定理
mathjax: true
---

## 题目描述

As a traveling salesman in a globalized world, Alan has always moved a lot. He almost never lived in the same town for more than a few years until his heart yearned for a different place.

However, this newest town is his favorite yet - it is just so colorful. Alan has recently moved to Colorville, a smallish city in between some really nice mountains. Here, Alan has finally decided to settle down and build himself a home - a nice big house to call his own.

In Colorville, many people have their own houses - each painted with a distinct pattern of colors such that no two houses look the same. Every wall consists of exactly n × n squares, each painted with a given color (windows and doors are also seen as unique “colors”). The walls of the houses are arranged in the shape of a regular m-gon, with a roof on top. According to the deep traditions of Colorville, the roofs should show the unity among Colorvillians, so all roofs in Colorville have the same color.


![](https://imgconvert.csdnimg.cn/aHR0cDovL2ljcGMudXBjLmVkdS5jbi91cGxvYWQvaW1hZ2UvMjAxODEwMTAvMjAxODEwMTAyMDE2MjJfNDgyNTIucG5n)

Of course, Alan wants to follow this custom to make sure he fits right in. However, there are so many possible designs to choose from. Can you tell Alan how many possible house designs there are? (Two house designs are obviously the same if they can be translated into each other just by rotation.)

## 输入

The input consists of:
• one line with three integers n, m, and c, where
– n (1 ≤ n ≤ 500) is the side length of every wall, i.e. every wall consists of n × n squares;
– m (3 ≤ m ≤ 500) is the number of corners of the regular polygon;
– c (1 ≤ c ≤ 500) the number of different colors.

## 输出

Output s where s is the number of possible different house designs. Since s can be very large, output s mod (10 ^ 9 + 7).

## 样例输入

```
1 3 1
```

## 样例输出

```
1
```

## 题解

这是一道经典的手镯问题，可转化为“用c^(n^2)种不同颜色的珠子穿成有m颗珠子的手镯，有多少种方案”。

由组合数学Polya定理易知答案为 $\frac{1}{m}\sum_{i=1}^{m}c^{n^{2}\cdot gcd(i,m)}$。由于求和过程涉及模运算，最后需要计算m的乘法逆元。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int mod = 1e9 + 7;

int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
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

int main()
{
    int n, m, c;
    ll ans = 0;
    scanf("%d%d%d", &n, &m, &c);
    for (int i = 1; i <= m; i++)
        ans = (ans + quick_pow(c, n * n * gcd(i, m))) % mod;
    ans = ans * quick_pow(m, mod - 2) % mod;
    printf("%lld\n", ans);
    return 0;
}
```