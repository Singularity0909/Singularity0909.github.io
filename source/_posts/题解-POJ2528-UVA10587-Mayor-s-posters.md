---
title: 题解 POJ2528 / UVA10587 Mayor's posters
date: 2019-08-15 02:25:02
categories: DS&A
tags:
- 线段树
- 离散化
thumbnail: https://cdn.jsdelivr.net/gh/singularity0909/cdn/img/gallery/icpc.jpg
---

{% pdf https://uva.onlinejudge.org/external/105/p10587.pdf %}

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

const int maxn = 1e4 + 10;
int id[maxn << 2];
bool vis[maxn];
struct intvl { int l, r; } itv[maxn];
struct node { int l, r, v; } tree[maxn << 4];

inline const int read()
{
    int x = 0, f = 1; char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}

inline int ls(int id) { return id << 1; }
inline int rs(int id) { return id << 1 | 1; }

void push_up(int id)
{
    if (tree[ls(id)].v == tree[rs(id)].v) tree[id].v = tree[ls(id)].v;
    else tree[id].v = 0;
}

void push_down(int id)
{
    if (tree[id].v) tree[ls(id)].v = tree[rs(id)].v = tree[id].v;
}

void build(int id, int l, int r)
{
    if ((tree[id].l = l) == (tree[id].r = r))
    {
        tree[id].v = 0;
        return;
    }
    int mid = (l + r) >> 1;
    build(ls(id), l, mid);
    build(rs(id), mid + 1, r);
}

void update(int id, int l, int r, int v)
{
    if (tree[id].l == l && tree[id].r == r)
    {
        tree[id].v = v;
        return;
    }
    push_down(id);
    int mid = (tree[id].l + tree[id].r) >> 1;
    if (r <= mid) update(ls(id), l, r, v);
    else if (l > mid) update(rs(id), l, r, v);
    else
    {
        update(ls(id), l, mid, v);
        update(rs(id), mid + 1, r, v);
    }
    push_up(id);
}

int query(int id, int l, int r)
{
    if (tree[id].v)
    {
        if (!vis[tree[id].v])
        {
            vis[tree[id].v] = true;
            return 1;
        }
        return 0;
    }
    if (l == r) return 0;
    int mid = (tree[id].l + tree[id].r) >> 1;
    return query(ls(id), l, mid) + query(rs(id), mid + 1, r);
}

int main()
{
    int t = read();
    while (t--)
    {
        int tot = 0, m = 0, n = read();
        memset(vis, false, sizeof(vis));
        for (int i = 0; i < n; i++)
        {
            int x = read(), y = read();
            itv[i].l = x; itv[i].r = y;
            id[tot++] = x; id[tot++] = y;
        }
        sort(id, id + tot);
        m = tot = unique(id, id + tot) - id;
        for (int i = 1; i < tot; i++)
            if (id[i] > id[i - 1] + 1)
                id[m++] = id[i - 1] + 1;
        sort(id, id + m);
        build(1, 1, m);
        for (int i = 0; i < n; i++)
        {
            int x = lower_bound(id, id + m, itv[i].l) - id + 1;
            int y = lower_bound(id, id + m, itv[i].r) - id + 1;
            update(1, x, y, i + 1);
        }
        printf("%d\n", query(1, 1, m));
    }
    return 0;
}
```