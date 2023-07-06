---
title: 洛谷 - P3870 [TJOI2009] 开关
permalink: luogu-p3870/
date: 2023-07-06 08:34:16
tags: 题解
---

## 题面
---
### 题意
有一个初始全部为 $0$ 的长度为 $n$ 的序列和 $m$ 次操作，每次操作会将区间 $[l, r]$ 内的 $0$ 变成 $1$，$1$ 变成 $0$ 或者询问区间中 $1$ 的数量。

### 输入格式
第一行有两个正整数 $n$ 和 $m$，分别表示序列的长度和操作次数。
接下来的 $m$ 行每行有三个整数 $op, l, r$。$op$ 为操作的种类，$[l, r]$ 为要询问或修改的区间。

- 当 $op$ 的值为 $0$ 时，表示第一种操作
- 当 $op$ 的值为 $1$ 时，表示第二种操作

### 数据范围
对于 $100\%$ 的数据，$2 \le n \le 10^5$，$1 \le m \le 10^5$，$1 \le l, r \le n$，$c \in {0, 1}$


## 思路
---
一看是区间问题，又看到了数据范围，发现这道题可以用线段树做。
我们只需要维护当前区间 $1$ 的数量即可。很容易发现当前区间 $1$ 的数量是其左边半个区间和右边半个区间中 $1$ 的数量的和。其他讲解见代码。


## 代码
---
```cpp
#include <bits/stdc++.h>

using std::cin;
using std::cout;
const char endl = '\n';

#define ls u << 1
#define rs u << 1 | 1

const int N = 1e5 + 5;

struct node {
    int l, r, len, s, d;

    node() {
        l = r = len = s = d = 0;
    }
    node(int _l, int _r) {
        l = _l, r = _r, len = r - l + 1;
        s = d = 0;
    }
} tr[N << 2];

int n, m, op, l, r;

inline void pushup(int u) {
    tr[u].s = tr[ls].s + tr[rs].s;
}

void build(int u, int l, int r) {
    tr[u] = node(l, r);

    if (l == r) return tr[u].s = 0, void();

    int mid = l + r >> 1;

    build(ls, l, mid);
    build(rs, mid + 1, r);

    pushup(u);
}

inline void pushdown(int u) {
    if (!tr[u].d) return;

    tr[ls].s = tr[ls].len - tr[ls].s;
    tr[rs].s = tr[rs].len - tr[rs].s;

    tr[ls].d ^= 1;      // 此处为将两个子树的懒标记取反，因为如果懒标记是 1，代表需要修改序列中的数，那么再修改一次就抵消了
    tr[rs].d ^= 1;

    tr[u].d = 0;
}

void modify(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].s = tr[u].len - tr[u].s, tr[u].d ^= 1, void();

    int mid = tr[u].l + tr[u].r >> 1;

    pushdown(u);

    if (l <= mid) modify(ls, l, r);
    if (r > mid) modify(rs, l, r);

    pushup(u);
}

int query(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].s;

    int mid = tr[u].l + tr[u].r >> 1, s = 0;

    pushdown(u);

    if (l <= mid) s += query(ls, l, r);
    if (r > mid) s += query(rs, l, r);

    return s;
}

int main() {
    std::ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);

    cin >> n >> m;

    build(1, 1, n);

    while (m --) {
        cin >> op >> l >> r;

        if (op == 0) modify(1, l, r);
        else cout << query(1, l, r) << endl;
    }

    return 0;
}
```