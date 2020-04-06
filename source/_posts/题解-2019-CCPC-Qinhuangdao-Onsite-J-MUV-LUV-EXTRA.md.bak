---
title: 题解 2019 CCPC Qinhuangdao Onsite J. MUV LUV EXTRA
date: 2019-11-24 10:46:18
tags:
- 最小循环节
- KMP
thumbnail: https://cdn.jsdelivr.net/gh/singularity0909/cdn/img/gallery/icpc.jpg
---

{% pdf https://codeforces.com/gym/102361/problem/J %}

观察样例不难发现，在最优解中，p为后缀循环节出现的总长度（可能包含不完整循环节），l为后缀最小循环节长度。

将序列逆序后枚举前缀，则p == i, l == i - next[i]，维护上式最大值即为答案。

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int inf = 0x3f3f3f3f;
const int maxn = 1e7 + 10;
int num[maxn], nxt[maxn];
char s[maxn];

inline const int read()
{
    int x = 0, f = 1; char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}

void getNext(int* v, int n)
{
    for (int i = 2, j = 0; i <= n; i++)
    {
        while (j && v[i] != v[j + 1]) j = nxt[j];
        if (v[i] == v[j + 1]) j++;
        nxt[i] = j;
    }
}

int main()
{
    int len = 0;
    ll a = read(), b = read(), ans = -inf;
    scanf("%s", s);
    for (int i = (int)strlen(s) - 1; s[i] != '.'; i--) num[++len] = s[i] - '0';
    getNext(num, len);
    for (int i = 1; i <= len; i++) ans = max(ans, i * a - (i - nxt[i]) * b);
    printf("%lld\n", ans);
    return 0;
}
```