---
title: 洛谷 - P3865 【模板】ST 表
permalink: luogu-p3864/
date: 2023-07-11 17:27:16
tags: 题解
---

## 题面
---
### 题意
给定一个长度为 $n$ 的序列，询问 $m$ 次区间最大值。

### 输入格式
第一行两个正整数 $n, m$，第二行 $n$ 个正整数表示序列。
接下来 $m$ 行每行两个正整数 $l, r$ 表示要询问区间 $[l,r]$ 内的最大值。

### 输出格式
共 $m$ 行第 $i$ 行为第 $i$ 次询问的答案。

### 数据范围
- 对于 $30\%$ 的数据，$1 \le n, m \le 10$。
- 对于 $70\%$ 的数据，$1 \le n, m \le 10^5$。
- 对于 $100\%$ 的数据，$1 \le n \le 10^5, 1 \le m \le 2 \times 10^6, a_i \in [0, 10^9], 1 \le l_i \le r_i \le n$。

## 思路
---
题目名称已经告诉我们此题正解为 ST 表，所以我们来用线段树来做，这道题不涉及修改操作所以用线段树维护还是比较简单的。
很容易想到当前区间最值是左右两个儿子的最值的最值，这样就很好维护了。至于查询，先判断当前区间是否在要查询的区间之内，然后判断是否全在左或右儿子，如果是就分别查找，否则就在两个儿子里查最值。

## 代码
---
```cpp
#include <bits/stdc++.h>

inline int read() {
    int x = 0, f = 1;
    char ch = getchar();

    while (ch < '0' || ch > '9') f = ch == '-' ? -1 : 1, ch = getchar();
    while (ch >= '0' && ch <= '9') x = (x << 1) + (x << 3) + (ch ^ '0'), ch = getchar();

    return x * f;
}
void _write(int x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) _write(x / 10);
    putchar(x % 10 + '0');
}
inline void write(int x, char ch) {
    _write(x), putchar(ch);
}

inline int max(int a, int b) {return a > b ? a : b;}

#define rep(i, l, r) for (int i = l; i <= r; ++ i)

#define ls u << 1
#define rs u << 1 | 1

const int N = 1e5 + 5;

struct node {
    int l, r, max;

    node()
        : l(0), r(0), max(0) {}
    
    node(int _l, int _r)
        : l(_l), r(_r), max(0) {}
} tr[N << 2];

int n, m, l, r, a[N];

inline void pushup(int u) {
    tr[u].max = max(tr[ls].max, tr[rs].max);
}

void build(int u, int l, int r) {
    tr[u] = node(l, r);
    if (l == r) return tr[u].max = a[l], void();

    int mid = l + r >> 1;
    build(ls, l, mid);
    build(rs, mid + 1, r);

    pushup(u);
}

int query(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].max;         // 完全被包含

    int mid = tr[u].l + tr[u].r >> 1;
    if (r <= mid) return query(ls, l, r);                       // 完全在左侧
    if (l > mid) return query(rs, l, r);                        // 完全在右侧

    return max(query(ls, l, mid), query(rs, mid + 1, r));       // 都不在，则分别查找
}

int main() {
    n = read(), m = read();
    rep(i, 1, n) a[i] = read();

    build(1, 1, n);

    while (m --) {
        l = read(), r = read();
        write(query(1, l, r), '\n');
    }

    return 0;
}
```