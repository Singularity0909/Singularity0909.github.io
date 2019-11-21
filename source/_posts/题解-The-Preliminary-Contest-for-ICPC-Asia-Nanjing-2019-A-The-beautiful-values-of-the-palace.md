---
title: '题解 The Preliminary Contest for ICPC Asia Nanjing 2019 A. The beautiful values
  of the palace'
date: 2019-11-21 21:19:01
tags:
- 扫描线
- 线段树
thumbnail: https://cdn.jsdelivr.net/gh/singularity0909/cdn/img/gallery/icpc.jpg
---

Here is a square matrix of n * n, each lattice has its value (n must be odd), and the center value is n * n. Its spiral decline along the center of the square matrix (the way of spiral decline is shown in the following figure:)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191023194724155.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191023194736382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1ODUwMTQ3,size_16,color_FFFFFF,t_70)
**The grid in the lower left corner is (1,1) and the grid in the upper right corner is (n , n)**

Now I can choose m squares to build palaces. The beauty of each palace is equal to the digital sum of the value of the land which it is located. Such as (the land value is 123213, the beautiful values of the palace located on it is  1 + 2 + 3 + 2 + 1 + 3 = 12) ( 666 666 ->  18 18) ( 456 456 -> 15 15).

Next, we ask p times to the sum of the beautiful values of the palace in the matrix where the lower left grid (x1, y1), the upper right square (x2, y2).

### Input

The first line has only one number T. Representing T-group of test data (T ≤ 5)

The next line is three number: n m p

The m lines follow, each line contains two integers the square of the palace (x, y)

The p lines follow, each line contains four integers: the lower left grid (x1, y1), the upper right square (x2, y2)

### Output

Next, p1 + p2 ... + pT lines: Represent the answer in turn. (n ≤ 10 ^ 6) (m, p ≤ 10 ^ 5) (n ≤ 10 ^ 6) (m, p ≤ 10 ^ 5)

**样例输入**

```
1
3 4 4
1 1
2 2
3 3
2 3
1 1 1 1
2 2 3 3
1 1 3 3
1 2 2 3
```

**样例输出**

```
5
18
23
17
```

### Solution

这是刚开学时南京站网络赛的一道题，当时是在学长的指导下写的。现场赛打铁后再来重温一下。

题目大意：给定一个大小为n * n，元素值从1开始自右上角顺时针螺旋递增的矩阵，查询在指定矩形范围内所有值的各位数之和。

这是二维扫描线，官方题解方法是离散化 + 二维前缀和，我的方法是离散化 + 线段树 + 离线操作。

将所有值所在的点坐标和所有查询的点坐标存入结构体按照位置和种类排序后，当遍历到某一次查询的左下角点时，利用线段树求在此查询左右范围内的和，当遍历到此次查询的右上角点时，再重复一次操作，两个和作差即此次查询的结果。

难点在于构造螺旋矩阵坐标对值的映射和定义所有点坐标的排序规则，具体代码实现如下。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
 
const int maxn = 1e6 + 10;
const int maxm = 1e5 + 10;
int t, n, m, p, cnt, ans[maxm];
struct node { int l, r, sum; } tree[maxn << 2];
struct cor { int x, y, type, qid; } cord[maxm << 2];
struct qes { int x1, x2; } qest[maxm];
// type == 0: square for update, type == 1: lower left square for query, type == 2: upper right square for query
 
inline const int read()
{
    int x = 0, f = 1; char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}
 
int getVal(ll x, ll y)
{
    x = x - n / 2 - 1;
    y = y - n / 2 - 1;
    ll tmp = max(abs(x), abs(y));
    ll num = x >= y ? (ll)n * n - tmp * tmp * 4 - tmp * 2 - x - y : (ll)n * n - tmp * tmp * 4 + tmp * 2 + x + y;
    int res = 0;
    do { res += num % 10; num /= 10; } while (num);
    return res;
}
 
bool cmp(const cor &a, const cor &b)
{
    if (a.y != b.y) return a.y < b.y;
    if (a.x != b.x) return a.x < b.x;
    if ((a.type == 0 && b.type == 1) || (a.type == 1 && b.type == 0)) return a.type > b.type;
    return a.type < b.type;
}
 
inline int ls(int id) {return id << 1;}
inline int rs(int id) {return id << 1 | 1;}
 
void push_up(int id)
{
    tree[id].sum = tree[ls(id)].sum + tree[rs(id)].sum;
}
 
void build(int id, int l, int r)
{
    tree[id].l = l; tree[id].r = r; tree[id].sum = 0;
    if (l == r) return;
    int mid = (l + r) >> 1;
    build(ls(id), l, mid);
    build(rs(id), mid + 1, r);
    push_up(id);
}
 
void update(int id, int l, int r, int v)
{
    if (tree[id].l == l && tree[id].r == r)
    {
        tree[id].sum += (r - l + 1) * v;
        return;
    }
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
    if (tree[id].l == l && tree[id].r == r) return tree[id].sum;
    int mid = (tree[id].l + tree[id].r) >> 1;
    if (r <= mid) return query(ls(id), l, r);
    if (l > mid) return query(rs(id), l, r);
    return query(ls(id), l, mid) + query(rs(id), mid + 1, r);
}
 
int main()
{
    t = read();
    while (t--)
    {
        cnt = 0;
        n = read(); m = read(); p = read();
        build(1, 1, n);
        for (int i = 0; i < m; i++)
        {
            int x = read(), y = read();
            cord[cnt++] = cor{x, y, 0, 0};
        }
        for (int i = 0; i < p; i++)
        {
            int x1 = read(), y1 = read(), x2 = read(), y2 = read();
            cord[cnt++] = cor{x1, y1, 1, i};
            cord[cnt++] = cor{x2, y2, 2, i};
            qest[i] = qes{x1, x2};
        }
        sort(cord, cord + cnt, cmp);
        for (int i = 0; i < cnt; i++)
        {
            int x = cord[i].x, y = cord[i].y, id = cord[i].qid;
            if (cord[i].type == 0) update(1, x, x, getVal(x, y));
            else if (cord[i].type == 1) ans[id] = query(1, qest[id].x1, qest[id].x2);
            else ans[id] = query(1, qest[id].x1, qest[id].x2) - ans[id];
        }
        for (int i = 0; i < p; i++) printf("%d\n", ans[i]);
    }
    return 0;
}
```