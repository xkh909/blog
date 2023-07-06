---
title: 洛谷 - P4168 [Violet] 蒲公英
permalink: luogu-p4168/
date: 2023-07-06 08:17:16
tags: 题解
---

## 题意

有一个长度为 $n$ 的序列 $a$ 和 $m$ 次询问，每次询问区间 $[l, r]$ 中的众数。因为要求在线，所以真正的 $l$ 和 $r$ 这样算（假设输入的是 $l_0$ 和 $r_0$）：$l = ((l_0 + x - 1) \bmod n) + 1, r = ((r_0 + x - 1) \bmod n) + 1$。若 $l > r$ 则交换 $l$ 和 $r$。

- 对于 $20\%$ 的数据，$n, m \le 3000$；
- 对于 $100\%$ 的数据，$1 \le n \le 40000$，$1 \le m \le 50000$，$1 \le a_i \le 10^9$，$1 \le l_0, r_0 \le n$。

## 思路

由于我懒得写正解，就想了个歪解出来。

首先将原序列离散化，缩小数据范围，离散化后就好办了，直接开一个桶存储询问区间的数出现的次数，然后再上个快读和 O2，这题也就过去了。

## 代码

```cpp
#include <bits/stdc++.h>

using std::cin;
using std::cout;
const char endl = '\n';

inline void swap(int &a, int &b) {a ^= b, b ^= a, a ^= b;}

inline int read() {
    int x = 0, f = 1;
    char ch = getchar();

    while (ch < '0' || ch > '9') f |= (ch == '-'),  ch = getchar();
    while (ch >= '0' && ch <= '9') x = (x << 1) + (x << 3) + (ch ^ '0'), ch = getchar();

    return x * f;
}

const int N = 4e4 + 5;

int n, m;
int l0, r0, l, r, max, maxn;
int a[N], b[N], t[N];

int main() {
    std::ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);

    n = read(), m = read();
    for (int i = 1; i <= n; ++ i) a[i] = b[i] = read();

    std::sort(b + 1, b + n + 1);
    int num = std::unique(b + 1, b + n + 1) - b - 1;

    for (int i = 1; i <= n; ++ i) a[i] = std::lower_bound(b + 1, b + num + 1, a[i]) - b;

    while (m --) {
        std::fill(t + 1, t + num + 1, 0);
        max = INT_MIN;

        l = (read() + b[maxn] - 1) % n + 1, r = (read() + b[maxn] - 1) % n + 1;
        if (l > r) swap(l, r);

        for (int i = l; i <= r; ++ i) ++ t[a[i]];

        for (int i = 1; i <= num; ++ i) {
            if (t[i] > max) max = t[i], maxn = i;
        }

        cout << b[maxn] << endl;
    }

    return 0;
}
```