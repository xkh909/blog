---
title: 一些代码
date: 2023-04-30 20:11:16
---
## 01
```cpp
/**
 * 线段树区间加 + 区间求和 + 区间最值
 * @date 2023-04-30
 */

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

ll query(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].s;
    
    int mid = tr[u].l + tr[u].r >> 1;
    ll s = 0;
    
    pushdown(u);
    
    if (l <= mid) s += query(ls, l, r);
    if (r > mid) s += query(rs, l, r);
    
    return s;
}

ll query_max(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].max;
    
    int mid = tr[u].l + tr[u].r >> 1;
    
    pushdown(u);
    
    if (r <= mid) return query_max(ls, l, r);
    if (l > mid) return query_max(rs, l, r);
    
    return max(query_max(ls, l, mid), query_max(rs, mid + 1, r));
}

ll query_min(int u, int l, int r) {
    if (tr[u].l >= l && tr[u].r <= r) return tr[u].min;
    
    int mid = tr[u].l + tr[u].r >> 1;
    
    pushdown(u);
    
    if (r <= mid) return query_min(ls, l, r);
    if (l > mid) return query_min(rs, l, r);
    
    return min(query_min(ls, l, mid), query_min(rs, mid + 1, r));
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
        else if (op == 2) cout << query(1, l, r) << endl;
        else if (op == 3) cout << query_max(1, l, r) << endl;
        else cout << query_min(1, l, r) << endl;
    }
    
    return 0;
}
```