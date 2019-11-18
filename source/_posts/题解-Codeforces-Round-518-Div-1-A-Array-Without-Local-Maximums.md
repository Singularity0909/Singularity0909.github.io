---
title: '题解 Codeforces Round #518 (Div. 1) A. Array Without Local Maximums'
date: 2019-08-12 12:41:27
categories: DS&A
tags: 数位DP
thumbnail: https://cdn.jsdelivr.net/gh/singularity0909/cdn/img/gallery/icpc.jpg
mathjax: true
---

# A. Array Without Local Maximums

time limit per test: 2 seconds
memory limit per test: 512 megabytes
inputstandard input
outputstandard output

Ivan unexpectedly saw a present from one of his previous birthdays. It is array of 𝑛 numbers from 1 to 200. Array is old and some numbers are hard to read. Ivan remembers that for all elements at least one of its neighbours ls not less than it, more formally:

𝑎1≤𝑎2,

𝑎𝑛≤𝑎𝑛−1 and

𝑎𝑖≤𝑚𝑎𝑥(𝑎𝑖−1,𝑎𝑖+1) for all 𝑖 from 2 to 𝑛−1.

Ivan does not remember the array and asks to find the number of ways to restore it. Restored elements also should be integers from 1 to 200. Since the number of ways can be big, print it modulo 998244353.

## Input

First line of input contains one integer 𝑛 (2≤𝑛≤105) — size of the array.

Second line of input contains 𝑛 integers 𝑎𝑖 — elements of array. Either 𝑎𝑖=−1 or 1≤𝑎𝑖≤200. 𝑎𝑖=−1 means that 𝑖-th element can't be read.

## Output

Print number of ways to restore the array modulo 998244353.

## Examples

#### input

    3
    1 -1 2

#### output

    1

#### input

    2
    -1 -1

#### output

    200

## Note

In the first example, only possible value of 𝑎2 is 2.

In the second example, 𝑎1=𝑎2 so there are 200 different values because all restored elements should be integers between 1 and 200.

## Solution

dp[i][j][k]表示当第i个数为j，第i-1个数与第i个数之间的大小关系为k时的方案数目。

(k = 0: a[i - 1] < a[i], k = 1: a[i - 1] = a[i], k = 2: a[i - 1] > a[i])

**状态转移方程**

dp[i][j][0] = $\sum_{x=1}^{j-1}$ (dp[i - 1][x][0] + dp[i - 1][x][1] + dp[i - 1][x][2])

dp[i][j][1] = dp[i - 1][j][0] + dp[i - 1][j][1] + dp[i - 1][j][2]

dp[i][j][2] = $\sum_{x=j+1}^{200}$ (dp[i - 1][x][1] + dp[i - 1][x][2])

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int maxn = 1e5 + 10;
const int mod = 998244353;
int n,  a[maxn];
ll sum, dp[maxn][201][3];

inline const int read()
{
    int x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9') { if (ch == '-') f = -1; ch = getchar(); }
    while (ch >= '0' && ch <= '9') { x = (x << 3) + (x << 1) + ch - '0'; ch = getchar(); }
    return x * f;
}

int main()
{
    n = read();
    for (int i = 1; i <= n; i++) a[i] = read();
    if (~a[1]) dp[1][a[1]][0] = 1;
    else for (int j = 1; j <= 200; j++) dp[1][j][0] = 1;
    for (int i = 2; i <= n; i++)
    {
        sum = 0;
        for (int j = 1; j <= 200; j++)
        {
            if (a[i] == -1 || a[i] == j)
            {
                dp[i][j][0] = sum;
                dp[i][j][1] = (dp[i - 1][j][0] + dp[i - 1][j][1] + dp[i - 1][j][2]) % mod;
            }
            sum = (sum + dp[i - 1][j][0] + dp[i - 1][j][1] + dp[i - 1][j][2]) % mod;
        }
        sum = 0;
        for (int j = 200; j >= 1; j--)
        {
            if (a[i] == -1 || a[i] == j) dp[i][j][2] = sum;
            sum = (sum + dp[i - 1][j][1] + dp[i - 1][j][2]) % mod;
        }
    }
    sum = 0;
    for (int j = 1; j <= 200; j++) sum = (sum + dp[n][j][1] + dp[n][j][2]) % mod;
    printf("%lld\n", sum);
    return 0;
}
```