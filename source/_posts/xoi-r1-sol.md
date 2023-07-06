---
title: XOI Round 1 题解
permalink: xoi-r1-sol/
date: 2023-07-06 16:10:15
tags: 题解
published: false
---
## 目录
---
[A 方程](#方程)
[B 区间](#区间)

---

## 方程
---
### 题面
#### 题意
输入一个形如 `ax^2 ± bx ± c = k` 的一元二次方程，要你求出它的实数解。保证输入的方程有实数解。

#### 输入格式
共一行，为原方程。为 $1$ 的系数不会被省略。

#### 输出格式
有若干行，第 $i$ 行为 `x_i = a` 的形式，表示方程的第 $i$ 组实数解为 $a$。输出时保留五位小数。

#### 样例
输入 #1
```text
1x^2 + 1x + 15 = 45
```
输出 #1
```text
x_1 = 5
x_2 = -6
```

#### 数据范围
对于 $100\%$ 的数据，保证输入的方程长度不超过 $100$ 且有实数解。$-10^5 \le a, b, c \le 10^5, 0 \le k \le 10^5$。不保证 $a, b, c, k$ 为整数。


### 思路
根据题面，我们很容易发现本题可以使用公式法求解，此处我就不再赘述了。
本题的主要难点在于读取未知数的系数，从而使用公式法求解。我这里使用的是 `scanf`。
其余讲解见代码。


### 代码
```cpp
#include <bits/stdc++.h>

using std::cin;
using std::cout;
const char endl = '\n';

char opb, opk, opc;
double a, b, k, c, d;

int main() {
    std::ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);

    scanf("%lfx^2 %c %lfx %c %lf = %lf", &a, &opb, &b, &opc, &c, &k);   // 此处将 b, c 和它们前面的运算符号分开读

    if (opb == '-') b = -b;
    if (opc == '-') c = -c;
    
    c -= k;
    d = b * b - 4 * a * c;
    
    cout << "x_1 = " << std::fixed << std::setprecision(5) << (-b + sqrt(d)) / (2 * a) << endl;
    cout << "x_2 = " << std::fixed << std::setprecision(5) << (-b - sqrt(d)) / (2 * a) << endl;

    return 0;
}
```

---

## 区间
### 题面
#### 题意
给定一个长度为 $n$ 的序列，有 $m$ 次操作，需要实现四个操作。
操作 1：区间加
操作 2：区间求和
操作 3：区间求最大值
操作 4：区间求最小值

#### 输入格式
第一行包含两个正整数 $n, m$，分别表示序列的长度和操作次数。
第二行包含 $n$ 个整数，为初始序列。
接下来的 $m$ 行，每行包含三个或四个正整数，表示一个操作。

#### 输出格式
输出包含若干行，为所有操作 2、3 和操作 4 的结果。

#### 样例
输入 #1
```text
5 4
5 4 4 4 5
1 3 3 -1
2 1 5
3 1 5
4 1 5
```
输出 #1
```text
21
5
3
```

#### 数据范围
- 对于 $10\%$ 的数据，$1 \le n, m \le 10^3$；
- 对于 $100\%$ 的数据，$1 \le n, m \le 10^5$，且保证任意时刻序列中的元素的绝对值 $\le 10^{18}$。


### 思路
根据题意很容易发现本题可以使用线段树解决，我们可以考虑维护区间和和区间最值。
很容易发现一个结点的区间和是其左儿子和右儿子的区间和之和，最值也为两个儿子的最值的最值。
区间和查询就不过多介绍了，说一下区间最值查询。查询最值的时候有这三种情况：当前结点的区间完全被要查询的区间包含，要查询的区间完全在当前结点的区间的左半部分和要查询的区间完全在当前结点的区间的右半部分。这样最值查询就很容易实现了。
其余讲解见代码。


### 代码
```cpp
#include <bits/stdc++.h>

using std::cin;
using std::cout;
const char endl = '\n';

#define ll long long

#define ls u << 1
#define rs u << 1 | 1

inline ll max(ll a, ll b) {return a > b ? a : b;}
inline ll min(ll a, ll b) {return a < b ? a : b;}

const int N = 1e5 + 5;

/**
 * 存储线段树
 * @param l, r      左右端点
 * @param s         区间和
 * @param max, min  区间最值
 * @param d         懒标记
 */

// 若不做特殊解释，下面的 u 均为父节点下标

struct node {
    int l, r;
    ll s, max, min, d;

    node() {
        l = r = s = max = min = d = 0;
    }
    node(int _l, int _r) {
        l = _l, r = _r;
        s = max = min = d = 0;
    }
} tr[N << 2];

int n, m, op, l, r;
ll k, a[N];

inline void pushup(int u) {
    tr[u].s = tr[ls].s + tr[rs].s;
    tr[u].max = max(tr[ls].max, tr[rs].max);
    tr[u].min = min(tr[ls].min, tr[rs].min);
}

inline void pushdown(int u) {
    if (!tr[u].d) return;

    tr[ls].d += tr[u].d;
    tr[rs].d += tr[u].d;

    tr[ls].s += (tr[ls].r - tr[ls].l + 1) * tr[u].d;
    tr[rs].s += (tr[rs].r - tr[rs].l + 1) * tr[u].d;

    tr[ls].max += tr[u].d;
    tr[rs].max += tr[u].d;

    tr[ls].min += tr[u].d;
    tr[rs].min += tr[u].d;

    tr[u].d = 0;
}

void build(int u, int l, int r) {
    tr[u] = node(l, r);
    
    if (l == r) return tr[u].s = tr[u].max = tr[u].min = a[l], void();

    int mid = l + r >> 1;

    build(ls, l, mid);
    build(rs, mid + 1, r);

    pushup(u);
}

void modify(int u, int l, int r, ll d) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].d += d, tr[u].s += (tr[u].r - tr[u].l + 1) * d, tr[u].max += d, tr[u].min += d, void();

    int mid = tr[u].l + tr[u].r >> 1;

    pushdown(u);
    
    if (l <= mid) modify(ls, l, r, d);
    if (r > mid) modify(rs, l, r, d);

    pushup(u);
}

ll query_sum(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].s;

    int mid = tr[u].l + tr[u].r >> 1;
    ll s = 0;

    pushdown(u);

    if (l <= mid) s += query_sum(ls, l, r);                         // 要查询的区间与当前结点的左半部分有交集
    if (r > mid) s += query_sum(rs, l, r);                          // 要查询的区间与当前节点的右半部分有交集

    return s;
}

ll query_max(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].max;

    int mid = tr[u].l + tr[u].r >> 1;

    pushdown(u);

    if (r <= mid) return query_max(ls, l, r);                       // 要查询的区间完全在当前结点的左半部分
    if (l > mid) return query_max(rs, l, r);                        // 要查询的区间完全在当前结点的右半部分

    return max(query_max(ls, l, mid), query_max(rs, mid + 1, r));   // 同时跨越了左右部分，则在左右儿子分别查询最值
}

ll query_min(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].min;

    int mid = tr[u].l + tr[u].r >> 1;

    pushdown(u);
    
    if (r <= mid) return query_min(ls, l, r);                       // 要查询的区间完全在当前结点的左半部分
    if (l > mid) return query_min(rs, l, r);                        // 要查询的区间完全在当前结点的右半部分

    return min(query_min(ls, l, mid), query_min(rs, mid + 1, r));   // 同时跨越了左右部分，则在左右儿子分别查询最值
}

int main() {
    std::ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);

    cin >> n >> m;
    for (int i = 1; i <= n; ++ i) cin >> a[i];

    build(1, 1, n);

    while (m --) {
        cin >> op >> l >> r;

        if (op == 1) cin >> k, modify(1, l, r, k);
        else if (op == 2) cout << query_sum(1, l, r) << endl;
        else if (op == 3) cout << query_max(1, l, r) << endl;
        else cout << query_min(1, l, r) << endl;
    }

    return 0;
}
```