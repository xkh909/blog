---
title: 洛谷 - P3865 【模板】ST 表
permalink: luogu-p3865/
date: 2023-02-25 19:21:16
tags: 题解
excerpt: 「ST 表能做的事线段树也能做」
---

## 题意

给你一个长度为 $n(1 \le n \le 10^5)$ 的序列，有 $m(1 \le m \le 2 \times 10^6)$ 次询问，每次询问指定区间的最大值。

## 思路

这道题目可以用 ST 表来做，题目名字都说了是 ST 表，但我们不用 ST 表。有一句话叫做：

> 「ST 表能做的事线段树也能做」

而且这道题目也没有涉及到区间修改，所以用线段树写还是很简单的。
所以让我们来用线段树来做这道题目吧！

如果你不会线段树，可以看看二南一位学长写的博客：[线段树学习笔记 - Baoshuo's OI Blog](https://oi.baoshuo.ren/segment-tree/)

### 存储线段树

```cpp
/**
 * 存储线段树
 * @param l   左端点
 * @param r   右端点
 * @param max 区间最大值
 */ 

const int N = 1e5 + 5;

struct node {
    int l, r, max;

    node() {
        l = r = max = 0;
    }
    node(int _l, int _r) {
        l = _l, r = _r;
        max = 0;
    }
} tr[N << 2];
```

### 上传（pushup）

我们知道，两个儿子节点的区间最大值中较大的一个即为父区间的最大值。
所以我们的 pushup 可以这样写

```cpp
/**
 * 上传信息
 * @param u 父节点下标
 */

#define ls u << 1
#define rs u << 1 | 1

inline void pushup(int u) {
    tr[u].max = max(tr[ls].max, tr[rs].max);
}
```

### 建树（build）

```cpp
/**
 * 建树
 * @param u 父节点下标
 * @param l 左端点
 * @param r 右端点
 */

void build(int u, int l, int r) {
    tr[u] = node(l, r);

    if (l == r) return tr[u].max = a[l], void();

    int mid = l + r >> 1;

    build(ls, l, mid);
    build(rs, mid + 1, r);

    pushup(u);
}
```

### 区间查询（query）

```cpp
/**
 * 区间查询
 * @param u 父节点
 * @param l 左端点
 * @param r 右端点
 */
int query(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].max;     // 如果被包含直接返回当前最大值

    int mid = tr[u].l + tr[u].r >> 1;

    if (r <= mid) return query(ls, l, r);                   // 完全在当前区间左侧
    if (l > mid) return query(rs, l, r);                    // 完全在当前区间右侧

    return max(query(ls, l, mid), query(rs, mid + 1, r));   // 在左子树和右子树进行查询
}
```

## 代码

```cpp
#define ls u << 1
#define rs u << 1 | 1

const int N = 1e5 + 5;

struct node {
    int l, r, max;

    node() {
        l = r = max = 0;
    }
    node(int _l, int _r) {
        l = _l, r = _r;
        max = 0;
    }
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
    if (tr[u].l == l && tr[u].r == r) return tr[u].max;

    int mid = tr[u].l + tr[u].r >> 1;

    if (r <= mid) return query(ls, l, r);
    if (l > mid) return query(rs, l, r);

    return max(query(ls, l, mid), query(rs, mid + 1, r));
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++ i) cin >> a[i];

    build(1, 1, n);

    while (m --) {
        cin >> l >> r;
        cout << query(1, l, r) << endl;
    }

    return 0;
}
```

由于这道题目时间卡得有点紧，所以如果用线段树需要加个快读才能过。